---
title: SQL and pipeline reference
layout: default
nav_order: 6
---

# SQL and pipeline reference

Every pattern this course teaches, in DuckDB 1.5.5, and nothing it does not. Each example was run on DuckDB 1.5.5
on small tables with the names shown (`orders`, `order_items`, `customers`, `lines`, …); change the names to yours.
Paths are always written from the project folder, `'data/raw/<file>'`, as every lab writes them.

The one sentence behind all of it: **no error is not the same as correct.** Say how many rows you expect before you
run a query, count after every join and every filter, and check every number against a second computation.

## 0. SQL inside a notebook or a script

Every notebook in the course starts with the same cell. It anchors the working folder to the project, so `'data/raw/…'` means
the same file wherever you opened the notebook from.

```python
import os
from pathlib import Path

import duckdb
import pandas as pd

pd.set_option("display.max_rows", 400)   # show every row of a result

PROJECT_ROOT = Path.cwd().parent if Path.cwd().name == "notebooks" else Path.cwd()
os.chdir(PROJECT_ROOT)
print("Working in:", Path.cwd())

con = duckdb.connect()                   # an in-memory database; it reads files in data/raw/ directly
```

```python
con.sql("SELECT COUNT(*) AS n FROM 'data/raw/online_retail.parquet'").df()        # a table, displayed
n = con.sql("SELECT COUNT(*) FROM 'data/raw/online_retail.parquet'").fetchone()[0]  # one number, in Python
```

A **view** is a saved query with a name. Write a filter once, as a view, and read from the view everywhere after:

```sql
CREATE OR REPLACE VIEW real_orders AS
SELECT * FROM orders
WHERE status IS DISTINCT FROM 'canceled';

SELECT COUNT(*) FROM real_orders;
```

## 1. The inspection reflex: every table, every time

Four things, before any number is computed. This is DS1's four-line pandas check, in SQL.

```sql
DESCRIBE SELECT * FROM 'data/raw/online_retail.parquet';     -- every column and the type DuckDB inferred
```

```sql
SUMMARIZE SELECT * FROM 'data/raw/online_retail.parquet';    -- per column: type, min, max, share of NULLs, …
```

The **key test**. The documentation claims one row is one *something*. Is the column that says so unique?

```sql
SELECT COUNT(*) AS n_rows, COUNT(DISTINCT order_id) AS n_keys FROM orders;              -- one-column key
SELECT COUNT(*) AS n_rows, COUNT(DISTINCT (order_id, item_seq)) AS n_keys FROM order_items;  -- composite key
```

If the two numbers differ, the key is not a key: find the rows.

```sql
SELECT order_id, item_seq, COUNT(*) AS copies
FROM order_items
GROUP BY order_id, item_seq
HAVING COUNT(*) > 1;
```

The **census** of a column: every value and how often it appears. All of it, never just the top few. It is how you
find the rows that are not what the table claims one row is: a total among the members, a test row, a typo.

```sql
SELECT status, COUNT(*) AS n
FROM orders
GROUP BY status
ORDER BY n DESC;
```

## 2. Reading rows: the four-clause `SELECT`

```sql
SELECT order_id, customer_id, order_date      -- which columns
FROM orders                                   -- which table
WHERE order_date >= DATE '2018-02-01'         -- which rows
ORDER BY order_date DESC                      -- in what order
LIMIT 10;                                     -- how many
```

Filters: `=`, `!=`, `<`, `>=`; `AND` and `OR` with parentheses (without them, `AND` binds first; Block 6 shows the check it breaks);
`IN ('a', 'b')`; `BETWEEN 1 AND 5` (both ends included); `LIKE 'A%'` (`%` is any text, `_` is one character).

```sql
SELECT * FROM orders
WHERE (status = 'delivered' OR status = 'shipped')
  AND order_date BETWEEN DATE '2018-01-01' AND DATE '2018-01-31';
```

Calculated columns get a name with `AS`. Division in DuckDB is exact: `7 / 2` is `3.5`; `7 // 2` is `3`.

```sql
SELECT invoice, qty * price AS line_value, ROUND(qty * price, 2) AS rounded
FROM lines;
```

`SELECT DISTINCT country FROM lines;` lists each value once, without counts. Prefer the census: the counts matter.

## 3. `NULL`: what "missing" does to each clause

`NULL` means *no value*. It is not zero and not an empty string, and it is not equal to anything, not even `NULL`.

| Where | What `NULL` does |
|---|---|
| `WHERE col = NULL` | never true: returns no rows. Write `col IS NULL` / `col IS NOT NULL`. |
| `WHERE col != 'x'` | drops the `NULL` rows too: `NULL != 'x'` is not true, it is `NULL`, and `WHERE` keeps only true. |
| `WHERE col NOT IN ('x', 'y')` | drops the `NULL` rows too; with a `NULL` inside the list it returns no rows at all. |
| `COUNT(*)` / `COUNT(col)` | `COUNT(*)` counts rows; `COUNT(col)` counts rows where `col` has a value. |
| `SUM`, `AVG`, `MIN`, `MAX` | skip `NULL`s. Eurostat's municipal waste for Austria, 2022 to 2024, is 803, 782 and `:` (not available) kg per inhabitant: `AVG` is 792.5, not 528.33. |
| `col + 1`, `col * price` | `NULL`: arithmetic with a missing value has no value. |
| `GROUP BY col` | the `NULL`s become one group of their own. |
| a join on `col` | a `NULL` key matches nothing. |

**The NULL-safe filter.** To exclude one value and keep the rows that have no value, say so. Any of these three:

```sql
SELECT COUNT(*) FROM orders WHERE status IS DISTINCT FROM 'canceled';
SELECT COUNT(*) FROM orders WHERE status != 'canceled' OR status IS NULL;
SELECT COUNT(*) FROM orders WHERE COALESCE(status, '') != 'canceled';
```

`COALESCE(a, b)` is `a` if it has a value, otherwise `b`. Use it to *label* a missing value in an output
(`COALESCE(region, 'no region')`), never to pretend a missing number is zero before an average.

## 4. Summarising: aggregates, `GROUP BY`, `HAVING`

A query is evaluated in this order, not in the order it is written:

```text
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
```

`GROUP BY` and the aggregates are Block 2's; `HAVING` is Block 3's, where it finds the keys that repeat.

`WHERE` filters rows before they are grouped; `HAVING` filters groups after. A condition on an aggregate can only
live in `HAVING` (DuckDB refuses it in `WHERE`: *WHERE clause cannot contain aggregates*).

A name made with `AS` is born in `SELECT`, which runs after `WHERE`, so standard SQL does not let `WHERE` use it.
DuckDB does, as a shortcut, for a name that is not an aggregate; and if the name is also a column's, `WHERE` quietly
uses the column. Write the expression again in `WHERE`: the query then means the same in any database.

```sql
SELECT country,
       COUNT(DISTINCT invoice) AS invoices,
       SUM(qty * price)        AS revenue
FROM lines
WHERE invoice_date >= TIMESTAMP '2010-01-01'   -- rows: before grouping
GROUP BY country
HAVING COUNT(DISTINCT invoice) > 1             -- groups: after
ORDER BY revenue DESC;
```

Every column in `SELECT` is either in `GROUP BY` or inside an aggregate. DuckDB refuses the query otherwise, and that
refusal is a help: it means the question was not precise.

**A share of a total** (Block 4). Divide each group's sum by the total, computed in a *scalar subquery*: a query in brackets
that returns one number. The denominator must be the same population as the numerator, so the subquery repeats the
outer query's `WHERE`, word for word:

```sql
SELECT country,
       SUM(qty * price) AS revenue,
       SUM(qty * price) / (SELECT SUM(qty * price)
                           FROM lines
                           WHERE invoice_date >= TIMESTAMP '2010-01-01') AS share   -- the same filter as below
FROM lines
WHERE invoice_date >= TIMESTAMP '2010-01-01'
GROUP BY country
ORDER BY revenue DESC;
```

The check: the shares add up to 1, to rounding. If they add up to less, the numerator's filter and the
denominator's are not the same.

**Dates, the minimum.**

```sql
SELECT date_trunc('month', invoice_date) AS month,      -- first moment of the month
       year(invoice_date)                AS yr,
       strftime(invoice_date, '%Y-%m')   AS month_label,  -- text, for display
       SUM(qty * price)                  AS revenue
FROM lines
GROUP BY 1, 2, 3                                          -- by position: the first three columns
ORDER BY month;
```

**The metric is a sentence first.** Write it with its filter, its measure, and its population, then the query, then
the number of rows you expect: *revenue is the sum of quantity times price over lines that are …, by month, for
2010: twelve rows.* A number that violates its sentence is wrong even when the query runs.

## 5. Types: what inference did

`DESCRIBE` is Block 1's; `CAST` and `TRY_CAST` are taught in Block 5, where cleaning needs them.

`DESCRIBE` shows the type DuckDB chose for each column by reading the values. In Online Retail, a stock code with a
letter in it (`85123A`) makes the whole `StockCode` column text, and an ID written with decimals (`13085.0`) comes back
as `DOUBLE`. `CAST` **refuses** a value it cannot convert, and names it, where a spreadsheet would quietly guess:

```sql
-- error: Conversion Error: Could not convert string '…' to INT32 (the code it names can change from run to run)
SELECT CAST(StockCode AS INTEGER) AS code FROM 'data/raw/online_retail.parquet';
```

`TRY_CAST(StockCode AS INTEGER)` returns `NULL` for that value instead, **silently**. So every `TRY_CAST` comes with the
query that lists what it swallowed:

```sql
SELECT StockCode, COUNT(*) AS n
FROM 'data/raw/online_retail.parquet'
WHERE TRY_CAST(StockCode AS INTEGER) IS NULL AND StockCode IS NOT NULL   -- had a value, and the cast made it nothing
GROUP BY StockCode
ORDER BY n DESC;
```

On the whole file it swallows 1,675 of the 4,632 codes, on 80,112 of the 525,461 lines, and `85123A`, a real product,
is the most frequent of them.

A date written in a known format: `strptime('17/03/2010', '%d/%m/%Y')`.

## 6. Joins: keeping the row count honest

**Relationship kinds.** One-to-one (an order and its delivery address); one-to-many (an order and its items);
many-to-many (reviews that cover several orders, orders with several reviews). A left row is repeated once for every
match it has on the right. A join to a table that is unique on the join key cannot multiply the left rows; a join to
anything that is not unique can.

```sql
SELECT o.order_id, o.order_date, c.state
FROM orders AS o
INNER JOIN customers AS c ON o.customer_id = c.customer_id;   -- only orders with a matching customer

SELECT o.order_id, c.state
FROM orders AS o
LEFT JOIN customers AS c USING (customer_id);                 -- every order, with NULL state where no match
```

`ON a.x = b.y` names both columns; `USING (x)` is shorthand when the column has the same name on both sides.
`RIGHT JOIN` is a `LEFT JOIN` written from the other side; `FULL JOIN` keeps the unmatched rows of both sides.

**The three counts, after every join.** Rows in the left table; rows in the result; distinct left keys in the result.

```sql
SELECT
  (SELECT COUNT(*) FROM orders)                           AS left_rows,
  COUNT(*)                                                AS result_rows,
  COUNT(DISTINCT o.order_id)                              AS left_keys_in_result
FROM orders AS o
JOIN order_items AS i USING (order_id);
```

Result rows above left rows: the join multiplied (fan-out). Left keys in the result below left rows: the join dropped
some. Say which you expected before you run it, and put the three counts in the commit message.

**The anti-join**: the left rows that found no match. It is how you see what an `INNER JOIN` threw away.

```sql
SELECT c.customer_id, c.state
FROM customers AS c
LEFT JOIN regions AS r ON c.state = r.state
WHERE r.state IS NULL;
```

**Keep the unmatched rows, and label them**, so that a breakdown still adds up to its total:

```sql
SELECT COALESCE(r.region, 'no region for ' || c.state) AS region,
       COUNT(*) AS customers
FROM customers AS c
LEFT JOIN regions AS r ON c.state = r.state
GROUP BY 1
ORDER BY customers DESC;
```

**Fan-out, and the two patterns that avoid it.** A measure must be counted once. Decide where the dimension you
group by lives.

*(a) The dimension is on the measure's own table: no join at all.* Items sold per seller:

```sql
SELECT seller_id, COUNT(*) AS items_sold
FROM order_items
GROUP BY seller_id;
```

*(b) The dimension is on a parent table: aggregate the measure to the parent's grain first, in a CTE, then join.*
Items per order, by customer state:

```sql
WITH order_size AS (                         -- one row per order
  SELECT order_id, COUNT(*) AS n_items
  FROM order_items
  GROUP BY order_id
)
SELECT c.state,
       COUNT(*)          AS orders,
       AVG(s.n_items)    AS avg_items_per_order
FROM order_size AS s
JOIN orders    AS o USING (order_id)         -- one order per order_size row
JOIN customers AS c USING (customer_id)      -- one customer per order
GROUP BY c.state;
```

A **CTE** (`WITH name AS (…)`) is a named step inside one query; several can be chained with commas, each reading the
ones before. `COUNT(DISTINCT …)` after a multiplying join is a patch that hides the fan-out; it does not fix a `SUM`.

## 7. Data that is not a table yet: JSON and APIs

An API is a URL that returns data instead of a page. Its answer is a **document**, and the work is to find the tables
hiding in it — **one table per grain**.

| Shape in the document | The table it hides |
|---|---|
| a list of records `[{…}, {…}]` | one row per record |
| a dictionary keyed by id `{"17": {…}, "18": {…}}` | one row per key; the key is a column |
| a dictionary of dictionaries `{"2017-01-02": {"USD": …, "GBP": …}, …}` | one row per outer key per inner key: `(date, currency, value)` |
| parallel lists `{"date": [...], "value": [...]}` | columns |
| a list inside a record `{"order_id": 1, "items": [{…}, {…}]}` | a child table at a finer grain, with the parent's key in every row |

DuckDB reads a list of records as a table. Nested objects become `STRUCT`s (read with a dot); lists inside a
record are unnested into rows of a child table:

```sql
-- the document: a list of orders; each has an order_id, a customer object, and a list of items
SELECT order_id, customer.country AS country
FROM read_json_auto('data/raw/api/orders.json');                  -- the orders table: one row per order

SELECT order_id, item.sku, item.qty
FROM (SELECT order_id, unnest(items) AS item
      FROM read_json_auto('data/raw/api/orders.json'));           -- the items table: one row per order and item
```

A document keyed by id is clearer as a short Python loop: one row per key, the key kept as a column.

```python
import json

doc = json.load(open("data/raw/api/states.json"))   # {"SP": {"name": "São Paulo", "region": "Southeast"}, …}
rows = []
for state, record in doc.items():
    rows.append((state, record["name"], record["region"]))
states = pd.DataFrame(rows, columns=["state", "name", "region"])
con.sql("CREATE OR REPLACE TABLE states AS SELECT * FROM states")   # DuckDB reads a pandas table by its name
```

**A rate has a direction, and the document says which.** A response that carries `"base": "EUR"` and a number under
another currency is telling you how many units of one are worth one of the other. Write the unit down before you use
the number (*… per …*), read it from the document's own metadata rather than from a field name, and check one
conversion by hand against the table's.

**Pagination.** Many APIs answer one page at a time and say so in a metadata part of the response. A loader that
reads page 1 of 4 returns a quarter of the truth and no error. Loop over every page, then reconcile the rows you have
against the total the API reported:

```python
records = []
page, pages = 1, 1
while page <= pages:
    meta, recs = json.load(open(f"data/raw/api/pages/page{page}.json"))   # [metadata, records]
    pages = meta["pages"]
    records.extend(recs)
    page += 1
assert len(records) == meta["total"], f"{len(records)} records loaded, the API reported {meta['total']}"
```

**Fetching a response, once.** Python's standard library is enough: build the URL with its parameters, name
yourself in a `User-Agent` header (some servers, Frankfurter among them, answer `403 Forbidden` to Python's default
one), set a timeout, check the status, and save the document exactly as it arrived:

```python
import json
import urllib.parse
import urllib.request
from pathlib import Path

params = urllib.parse.urlencode({"base": "EUR", "symbols": "USD"})
url = f"https://api.frankfurter.dev/v1/2018-01-02?{params}"
request = urllib.request.Request(url, headers={"User-Agent": "ecbs5294-course (student project)"})
with urllib.request.urlopen(request, timeout=30) as response:
    assert response.status == 200, f"HTTP {response.status} from {url}"
    doc = json.load(response)
Path("data/raw/api").mkdir(parents=True, exist_ok=True)
Path("data/raw/api/eur_usd_2018-01-02.json").write_text(json.dumps(doc, indent=1))
```

**Offline first.** A response is fetched once, saved under `data/raw/api/` with the URL and the date in `DATA.md`,
and the analysis reads the saved copy. Be polite to public servers: cache, never loop against them, no keys in code.

## 8. Pipelines: bronze, silver, gold

```text
data/raw/  (bronze)   the files as they arrived. Committed, never edited.
data/silver/          typed, keyed, one row per stated grain, validated. Made by scripts/clean.py. Not committed.
data/gold/            the answer tables, at the question's grain. Made by scripts/report.py. Not committed.
pipeline.py           runs clean.py, report.py, checks.py in order, from the project folder.
```

**Idempotent:** run it twice, get the same tables. Each step rebuilds its output from the layer below
(`CREATE OR REPLACE TABLE …`, `COPY … TO` a file it overwrites); it never appends to what the last run left. A
mistake in silver is always recoverable, because raw never changed: fix the script and rerun.

**The cleaning moves, in SQL.** Read raw files as text first, so that nothing is converted before a step you control:

```sql
CREATE OR REPLACE TABLE bronze AS
SELECT * FROM read_csv('data/raw/file.tsv', delim = '\t', header = true, all_varchar = true);
```

Split a packed column into its parts, and turn years-as-columns into one row per observation:

```sql
CREATE OR REPLACE TABLE long AS
SELECT *
FROM (
  SELECT split_part("freq,unit,geo\TIME_PERIOD", ',', 2) AS unit,
         split_part("freq,unit,geo\TIME_PERIOD", ',', 3) AS geo,
         * EXCLUDE ("freq,unit,geo\TIME_PERIOD")
  FROM bronze
)
UNPIVOT INCLUDE NULLS (raw_value FOR year IN (COLUMNS('^[0-9]{4}$')));
```

`INCLUDE NULLS` keeps the empty cells. Without it, `UNPIVOT` silently drops every observation whose cell is empty, and
the accounting below never sees them: rows in and rows out would still agree, because the missing rows were never
counted in.

Then the moves on a text value, each one a decision you can count:

| Move | Expression | Say what it did |
|---|---|---|
| strip spaces | `TRIM(x)` | |
| a sentinel becomes missing | `NULLIF(x, ':')` | how many `:` there were |
| a value and the flag glued to it, apart | `split_part(x, ' ', 1)`, `split_part(x, ' ', 2)` | the census of the flags: a flag is information, **keep it** in its own column |
| text becomes a number | `TRY_CAST(x AS DOUBLE)` | the rows it turned into `NULL` (§5) |
| categories made comparable | `LOWER(TRIM(x))` | the census before and after |

**Units are a filter**: a silver table has one
unit, named in the table or its dictionary. Rows that are totals of other rows (a region among its members) are
flagged, `is_aggregate`, and kept out of any sum of the members.

**The accounting.** At the grain of one observation, every row lands in exactly one place, in this order: a value
that cannot be read is *rejected* (with a reason, to `rejects.csv`); another unit is *excluded*; a total of other
rows is *aggregate*; the rest is *retained* (a documented missing value, `:`, is retained as `NULL`). Print it on
every run:

```text
observations in = rejected + excluded + aggregate + retained
```

`CASE` tests its `WHEN`s in order and stops at the first that is true, so the precedence is the order you write
them in. Here the cast is the plain one, so the accounting shows what it could not read. The empty cell comes first,
and on its own line: its `raw_value` is `NULL`, and `NULL != ':'` is not true but `NULL`, so a rule written only as
`TRIM(raw_value) != ':'` would let an empty cell fall through to *retained*.

```sql
CREATE OR REPLACE TABLE observed AS
SELECT *,
       CASE
         WHEN raw_value IS NULL OR TRIM(raw_value) = '' THEN 'rejected'    -- an empty cell
         WHEN value IS NULL AND TRIM(raw_value) != ':'  THEN 'rejected'    -- had something, and it did not cast
         WHEN unit != 'THS_T'                           THEN 'excluded'    -- not the unit this table keeps
         WHEN geo IN ('EU27_2020', 'EA20')              THEN 'aggregate'   -- a total of other rows
         ELSE 'retained'                                                   -- including ':', kept as NULL
       END AS destination,
       CASE
         WHEN raw_value IS NULL OR TRIM(raw_value) = '' THEN 'empty cell'
         ELSE 'value is not a number: ' || raw_value
       END AS reason                                                       -- read only for the rejected rows
FROM (SELECT *, TRY_CAST(NULLIF(TRIM(raw_value), ':') AS DOUBLE) AS value FROM long);

SELECT destination, COUNT(*) AS observations
FROM observed
GROUP BY destination
ORDER BY destination;

SELECT (SELECT COUNT(*) FROM bronze) * 2                 AS expected_in,   -- raw rows × the file's 2 year columns
       COUNT(*)                                          AS observations_in,
       COUNT(*) FILTER (WHERE destination = 'rejected')  AS rejected,
       COUNT(*) FILTER (WHERE destination = 'excluded')  AS excluded,
       COUNT(*) FILTER (WHERE destination = 'aggregate') AS aggregate,
       COUNT(*) FILTER (WHERE destination = 'retained')  AS retained
FROM observed;

COPY (SELECT * FROM observed WHERE destination = 'rejected')
TO 'data/silver/rejects.csv' (HEADER);
```

The reason has its own `CASE` for the same trap: `'value is not a number: ' || raw_value` is `NULL` when `raw_value`
is, and a rejected row with no reason is a row nobody can act on.

A `CASE` with an `ELSE` puts every row somewhere, so the four destinations always add up to `observations_in`. The
check with teeth is the first column: the observations counted from the raw file, independently, must equal
`observations_in`. And the rejected rows, read one by one, are how you find a flag you had not seen.

**Persist** silver and gold as files the next step reads:

```sql
COPY (SELECT * FROM long) TO 'data/silver/long.parquet' (FORMAT parquet);
```

**Keep the latest row per key**, without window functions: find the latest date per key, then join back.

```sql
WITH latest AS (
  SELECT customer_id, MAX(order_date) AS order_date
  FROM orders
  GROUP BY customer_id
)
SELECT o.*
FROM orders AS o
JOIN latest AS l USING (customer_id, order_date);
```

**The data dictionary** (one per silver table, in `docs/dictionary.md`, a tracked file): column, type, meaning,
unit, allowed values, what a missing value means, and what cleaning did to it.

## 9. Validations as code

A check is an assertion that **stops the run**, with the number in its message. Not a print; not a comment.

The examples below are on a silver table `emissions`, one row per `(geo, year, sector)`, and its gold table
`by_country`, one row per `(geo, year)`.

```python
def one(sql):
    return con.sql(sql).fetchone()[0]

dupes = one("SELECT COUNT(*) - COUNT(DISTINCT (geo, year, sector)) FROM emissions")
assert dupes == 0, f"{dupes} duplicate (geo, year, sector) rows in silver emissions"
```

The four families, each a query whose answer must be a known number:

```sql
-- key: unique, on the key of THIS stage, and never NULL
SELECT COUNT(*) - COUNT(DISTINCT (geo, year, sector)) AS duplicates,
       COUNT(*) - COUNT(geo) AS null_geo
FROM emissions;

-- domain: only allowed values, only sensible ranges (rows outside are quarantined, with a reason)
SELECT COUNT(*) FROM emissions WHERE value < 0;
SELECT unit, COUNT(*) FROM emissions WHERE unit != 'MIO_T' GROUP BY unit;

-- count: rows in = rows out + rows rejected, and no gap that a GROUP BY would hide (it shows only groups that exist)
SELECT geo, COUNT(DISTINCT year) AS years_present
FROM emissions
GROUP BY geo
HAVING COUNT(DISTINCT year) != (SELECT COUNT(DISTINCT year) FROM emissions);

-- reconciliation: two independent computations agree (gold's total equals silver's)
SELECT (SELECT SUM(value) FROM emissions) - (SELECT SUM(total) FROM by_country) AS residual;
```

**A row you reject and a run you stop are different decisions.** An invalid row (an unreadable value, a date
outside the extract) is quarantined to `rejects.csv` with a reason, counted, and the run continues. A broken
invariant (a duplicate key in silver, a breakdown that does not sum to its total, more rejects than the stated
tolerance) stops the run. Stop at the first failed check, with the number in the message.

**Checks that pass on wrong data.** For every check, name the wrong data it would pass:

| Kind | Example | What it lets through |
|---|---|---|
| incorrect | a key check that only tests `geo IS NOT NULL` | every duplicate |
| weak | `assert n > 0` | anything but an empty table |
| tautological | gold's total compared with gold's total; a check that reuses the query it checks | everything |
| missing | no count check at all | a filter that drops a third of the rows |

**The fresh-clone test**, before you submit a pipeline:

```bash
git clone <your repository> fresh-copy
cd fresh-copy
uv sync
uv run python pipeline.py
```

## 10. Reconciliation: which identity the number permits

Every number is checked against an independent computation, with the identity the metric actually permits, and a
stated tolerance (to the cent for money; to the source's rounding for tonnes and people).

| The metric is… | The identity |
|---|---|
| **additive** (revenue, tonnes, orders) | the breakdown sums to a total computed by different code |
| **a ratio** (revenue per customer, tonnes per person, a converted total) | numerator and denominator reconciled separately, on the same population |
| **an average** (score by state, days to deliver) | recombines only with its weights: the overall mean is `SUM(n × mean) / SUM(n)`; averages do not add, and the average of averages is not the average |
| **the same quantity from two sources** | a residual, stated as a number, explained as far as the documentation goes and otherwise reported as unexplained |

```sql
WITH by_state AS (
  SELECT c.state, COUNT(*) AS n_items, AVG(i.price) AS mean_price
  FROM order_items AS i
  JOIN orders    AS o USING (order_id)
  JOIN customers AS c USING (customer_id)
  GROUP BY c.state
)
SELECT SUM(n_items * mean_price) / SUM(n_items) AS recombined,   -- equals the next line, to rounding
       (SELECT AVG(price) FROM order_items)     AS overall
FROM by_state;
```

**Compare with a tolerance, never with `=`.** Prices read from a file are usually `DOUBLE`, a binary fraction, and a
sum of `DOUBLE`s depends on the order it was added up in. The same three prices, added in two orders:

```sql
SELECT (SELECT SUM(p) FROM (VALUES (0.1::DOUBLE), (0.2::DOUBLE), (0.3::DOUBLE)) AS t(p)) AS one_order,   -- 0.6000000000000001
       (SELECT SUM(p) FROM (VALUES (0.3::DOUBLE), (0.2::DOUBLE), (0.1::DOUBLE)) AS t(p)) AS other_order; -- 0.6
```

On the whole Online Retail file, `SUM(Quantity * Price)` and the sum of the by-country sums differ in the sixth
or seventh decimal place, and the digits change with the number of processor threads DuckDB happens to use. Both are
9,539,484.63 to the penny. So a reconciliation compares within its tolerance — to the cent for money — and prints
the difference when it fails:

```python
total = con.sql("SELECT SUM(qty * price) FROM lines").fetchone()[0]
by_country = con.sql(
    "SELECT SUM(revenue) FROM (SELECT country, SUM(qty * price) AS revenue FROM lines GROUP BY country)"
).fetchone()[0]
assert abs(total - by_country) < 0.005, (
    f"by country {by_country:,.2f} against total {total:,.2f}: off by {by_country - total:.6f}"
)
```

In SQL the same test is `abs(a - b) < 0.005`, or both sides rounded, `ROUND(a, 2) = ROUND(b, 2)`. A number typed into a
query, such as `4.50`, is an exact `DECIMAL` in DuckDB; that is why the example above casts to `DOUBLE`, the type of
a price read from a file.

## 11. The note that goes with the number

Five to eight plain sentences for a reader who does not know SQL and will make a decision:

1. the metric, with its grain, its filter, and its unit;
2. the rows excluded, and how many;
3. the assumptions;
4. the reconciliation, stated: which two numbers agree;
5. **what the data cannot answer**, as a claim about the data: *"this cannot tell you X, because the data has no Y"* —
   not "more research is needed".

## 12. Asking an assistant about a query

**AI writes SQL that runs. Running is not right.** The counts are yours to run.

```text
1. Give it the schema (paste DESCRIBE) and the grain of each table, in words.
2. Give it the number you expected and the number you got.
3. Ask for the checks that would tell them apart, not for the fix.
4. Run the checks yourself. Paste what they returned.
5. Make one change. Reconcile. Explain it in your own words.
```

## Not in this course

Window functions (`OVER`, `ROW_NUMBER`, `LAG`, `QUALIFY`); subqueries more than one level deep; indexes and query
tuning; database servers, transactions, permissions; pandas as an analysis tool; regular expressions beyond one
pattern; dbt, Airflow, Spark. They exist, and the analytics and Data Engineering electives teach some of them. Every
question this course asks can be answered without them.
