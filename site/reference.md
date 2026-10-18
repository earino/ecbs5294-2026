---
title: SQL reference
layout: default
nav_order: 6
---

# SQL reference

Every pattern this course teaches, in DuckDB 1.5.5, and nothing it does not. Each example was run on DuckDB 1.5.5
on small tables with the names shown (`orders`, `order_items`, `customers`, `lines`, …); change the names to yours.
Paths are always written from the project folder, `'data/raw/<file>'`, as every lab writes them.

**If you know what you want but not what it is called:**

| I want… | Write… | Section |
|---|---|---|
| the first character of a code (an invoice number, a series key) | `left(Invoice, 1)` | 2, *Text* |
| the rows where a value is missing, or not missing | `WHERE col IS NULL` / `IS NOT NULL` — never `= NULL`, never `'NaN'` | 3 |
| to know whether this column is a key | `COUNT(*)` against `COUNT(DISTINCT col)` | 1 |
| every distinct value, with how often it occurs | the census: `GROUP BY col ORDER BY n DESC` | 1 |
| one row per month, or per year | `date_trunc('month', ts)` / `year(ts)` in the `SELECT` and the `GROUP BY` | 4 |
| a filter written once and reused | `CREATE OR REPLACE VIEW name AS SELECT … WHERE …` | 0 |
| one number into a Python variable | `.fetchone()[0]` | 0 |
| several rows, then a number from them | a `WHERE` or a query around the query, then `.fetchone()[0]` | 0 |
| to keep the rows that match either of two things | `WHERE a OR b`; both: `AND`; mixed: parentheses | 2 |
| a number that means "missing" turned into a label | `COALESCE(col, 'no value')` | 3 |
| to know how many invoices, not how many lines | `COUNT(DISTINCT Invoice)` | 4 |
| a check that two sums agree | `abs(a - b) < tolerance`, never `==` | 9 |
| the rows a join lost | `LEFT JOIN … WHERE right.key IS NULL` | 6 |
| the keys that repeat | `GROUP BY key HAVING COUNT(*) > 1` | 4, 6 |
| text turned into a number without a silent `NULL` | `CAST`, then count what `TRY_CAST` swallowed | 5, 8 |

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

**Several rows, then a number from them.** The result you looked at has many rows and you want one figure out of
it, or all of them added up. Ask SQL: the labs never need pandas for this.

```python
by_year = con.sql("SELECT year, SUM(co2) AS co2 FROM 'data/raw/countries.csv' GROUP BY year ORDER BY year").df()
by_year                                                                                           # look at it
one = con.sql("SELECT SUM(co2) FROM 'data/raw/countries.csv' WHERE year = 2024").fetchone()[0]    # one group's value
every = con.sql("""
    SELECT SUM(co2) FROM (SELECT year, SUM(co2) AS co2 FROM 'data/raw/countries.csv' GROUP BY year)
""").fetchone()[0]                                                                              # every group, added up
```

The pandas route reads the dataframe you already have. It is the same numbers; the course does not require it,
and it is one more thing to get wrong on a Tuesday:

```python
by_year.loc[by_year.year == 2024, "co2"].sum()    # one group's value
by_year.co2.sum()                                 # every group, added up
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

Filters: `=`, `!=`, `<`, `>=`; `AND` and `OR` with parentheses (without them, `AND` binds first);
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

### Text: the first character, and the rest

The filter Lab 2 turns on is one character of the invoice number. Every one of these was run on DuckDB 1.5.5:

```sql
SELECT left(Invoice, 1)          AS first_char,     -- '4', '5', 'C', 'A': the census of this is Lab 2's section A
       right(Invoice, 2)         AS last_two,
       substr(Invoice, 2, 3)     AS chars_2_to_4,   -- start at position 2, take 3
       length(Invoice)           AS n_chars,
       upper(Country), lower(Country), TRIM('  x  ')  AS trimmed,
       Invoice || '-' || Country AS glued
FROM 'data/raw/online_retail.parquet' LIMIT 1;
```

`split_part(text, ',', 2)` takes the second comma-separated piece (Block 6's labs unpack Eurostat's key column with it, in a supplied cell).
`LIKE 'C%'` (`%` is any text) also matches an invoice that starts with `C`; the course writes `left(Invoice, 1) = 'C'`,
because a character position says what it means and a pattern does not.

## 3. `NULL`: what "missing" does to each clause

`NULL` means *no value*. It is not zero and not an empty string, and it is not equal to anything, not even `NULL`.

**What you see on screen.** Every result in a notebook is displayed by pandas, and pandas has its own words for a
missing value: `NaN` in some columns, `None` in others (which one depends on what else the column holds and on the
pandas version). Neither word is in the file, and neither is a value: `WHERE iso_code = 'NaN'` returns no rows, and
`WHERE iso_code != 'NaN'` looks right only by accident, because every row with a missing code fails that test too.
What pandas prints as `NaN` or `None` is SQL's `NULL`, and the only test for it is `IS NULL`.

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

`GROUP BY` and the aggregates are Block 2's; `HAVING` is Block 4's, where it finds the keys that repeat.

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

**A share of a total.** Divide each group's sum by the total, computed in a *scalar subquery*: a query in brackets
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

`DESCRIBE` is Block 1's; `CAST` and `TRY_CAST` are taught in Block 6, where silver needs them.

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

## 6. Joins: two tables, one row

A join puts columns from two tables in one row, matched on a column they share. `FROM` one table, `JOIN` the other,
`ON` the columns that must be equal. Give each table a short name with `AS`, and say which table every column comes
from.

```sql
SELECT o.order_id, o.order_status, c.customer_state
FROM orders AS o
JOIN customers AS c ON c.customer_id = o.customer_id;     -- JOIN is INNER JOIN: only the rows that found a partner

SELECT o.order_id, i.price
FROM orders AS o
LEFT JOIN order_items AS i ON i.order_id = o.order_id;    -- every order; NULL price where an order has no items
```

`USING (x)` is shorthand for `ON a.x = b.x` when the column has the same name in both tables; the course writes `ON`.
`RIGHT JOIN` is a `LEFT JOIN` written from the other side; `FULL JOIN` keeps the rows with no partner on both sides.
Write a `LEFT JOIN` from the side you mean.

**After every join: what is one row now?** A row of the left table appears once for every partner it has. If every
row has exactly one partner (an order and its customer), one row is still one order. If a row can have several (an
order and its items), one row of the result is one item; with `LEFT JOIN`, an order with no items is one row too. A table that is unique on the join key cannot repeat the
left table's rows; a table that is not unique can.

**The three counts, after every join.** The rows that went in; the rows that came out; the distinct keys that came
out.

```sql
SELECT COUNT(*) FROM orders;                  -- the rows that went in

SELECT COUNT(*)                   AS rows_in_result,
       COUNT(DISTINCT o.order_id) AS distinct_orders_in_result
FROM orders AS o
JOIN order_items AS i ON i.order_id = o.order_id;
```

With `JOIN`, and a key that is unique in the table you start from, all three equal means every row found exactly one partner. More rows out than distinct keys: some rows
have several partners, and one row of the result is no longer one row of the left table (other texts call this
*fan-out*). Fewer distinct keys out than rows in: some rows found no partner, and `JOIN` left them out. Say which you
expect before you run it. A key made of two columns is counted as a pair: `COUNT(DISTINCT (order_id, order_item_id))`.

With `LEFT JOIN` the three counts say less: every left row is kept whether it found a partner or not, so three equal
counts mean only that each left row is there once. To know how many found no partner, count the rows where the right
table's key is `NULL`.

**The rows with no partner** (other texts call this an *anti-join*): `LEFT JOIN`, then keep the rows where the right
table's key is `NULL`. Test the side that may be missing.

```sql
SELECT o.order_id, o.order_status
FROM orders AS o
LEFT JOIN order_items AS i ON i.order_id = o.order_id
WHERE i.order_id IS NULL;
```

**Keep the rows with no partner, and label them**, so that a breakdown still adds up to its total:

```sql
SELECT COALESCE(t.product_category_name_english, 'no English name') AS category,
       COUNT(*)     AS items,
       SUM(i.price) AS item_sales
FROM order_items AS i
JOIN products AS p ON p.product_id = i.product_id
LEFT JOIN product_category_name_translation AS t ON t.product_category_name = p.product_category_name
GROUP BY category
ORDER BY item_sales DESC;
```

**When one row has several partners: one row per order first** (Block 4). `COUNT(*)` counts the rows of the result,
and `AVG` averages over them, whatever the column is called. If the question is about orders and the number is on a
table with several rows per order, add it up to one row per order first, then join. A **CTE** (`WITH name AS (…)`)
is a named step inside one query; several can be chained with commas, each reading the ones before.

```sql
WITH order_size AS (                          -- one row per order
  SELECT order_id, COUNT(*) AS n_items
  FROM order_items
  GROUP BY order_id
)
SELECT c.customer_state,
       COUNT(*)       AS orders,
       AVG(s.n_items) AS items_per_order
FROM order_size AS s
JOIN orders    AS o ON o.order_id = s.order_id          -- one order per order_size row
JOIN customers AS c ON c.customer_id = o.customer_id    -- one customer per order
GROUP BY c.customer_state;
```

If everything the question needs is on one table, do not join at all: items sold per seller is `order_items` alone.
`COUNT(DISTINCT …)` after a join that repeats rows repairs that one count; it does not repair an `AVG` or a `SUM`,
and `SUM(DISTINCT price)` adds up different *values*, not different rows.

## 7. Did every record arrive? An API's pages (Block 5)

An **API** is a web address that answers with data. The World Bank's answer is a **JSON** document, sent a page at a
time; the course saves each page to a file once, under `data/raw/api/`, and never calls a server in class.

**JSON in plain words.** `{…}` is a record of named values; `[…]` is a list. **What DuckDB does with any JSON
file:** `read_json_auto` reads it; `->>'name'` takes a named value out of a record, as text; `->'name'` goes one level
into a record and is still JSON; `unnest` turns a list into rows. **What this API does:** each page is a list of two
things, the **metadata** (which page, how many pages, how many records per page, the `total` in the whole answer),
then up to 1,000 records; and a series comes as several pages.

**Supplied: the setup that turns the pages into tables.** Every lab and homework that reads the World Bank's pages
has it in a setup cell. Read it once; you are never asked to write it.

```sql
CREATE OR REPLACE VIEW gdp_pages AS                     -- every page: the * reads page1, page2, ...
SELECT json FROM read_json_auto('data/raw/api/worldbank/NY.GDP.MKTP.PP.KD_page*.json');

CREATE OR REPLACE VIEW gdp_records AS                   -- one row per record: the list on each page, unnested
SELECT unnest(CAST(json AS JSON[])) AS record FROM gdp_pages WHERE json_type(json) = 'ARRAY';

CREATE OR REPLACE VIEW gdp AS                           -- the table: the fields taken out, made numbers
SELECT record->>'countryiso3code'      AS iso3,
       record->'country'->>'value'     AS country,
       CAST(record->>'date' AS INTEGER) AS year,
       CAST(record->>'value' AS DOUBLE) AS gdp
FROM gdp_records;

CREATE OR REPLACE VIEW gdp_metadata AS                  -- one row per page: what it says about the whole answer
SELECT CAST(json->>'page' AS INTEGER) AS page, CAST(json->>'pages' AS INTEGER) AS pages,
       CAST(json->>'per_page' AS INTEGER) AS per_page, CAST(json->>'total' AS INTEGER) AS total
FROM gdp_pages WHERE json_type(json) = 'OBJECT';
```

**What you write: the `*`, and the count against the total.** A `*` in a file name reads every file that matches:
`page*.json` reads all four pages, `page1.json` only the first. A table built from page 1 alone runs, with no error,
and holds 1,000 of 3,975. So count it against what the pages say the whole answer holds:

```sql
SELECT (SELECT COUNT(*) FROM gdp)                       AS rows_in_gdp,
       (SELECT total FROM gdp_metadata WHERE page = 1)  AS total_stated;   -- 3975 and 3975
```

**Places and groups.** The World Bank's table mixes places with groups (World, the regions, the income groups): the
first row of a ranking is `World`. Its list of countries (`country_page1.json`, the view `places` in the labs) marks
each group with the region `Aggregates`; a question about places joins to `places`, and a count of the places a table
holds is `COUNT(DISTINCT iso3)` of the rows that found a partner in `places`.

## 8. Bronze, silver, gold (Block 6)

| Layer | What it is | In Block 6 |
|---|---|---|
| **bronze** | the data exactly as it arrived, never edited: every later table can be rebuilt from it | the World Bank's pages, read into `gdp_bronze` and `population_bronze`: one row per record, every column text |
| **silver** | cleaned and checked: the right types, consistent and explicit units, duplicates and missing values handled, sometimes joined; still at the detail of the records | `gdp_silver` and `population_silver`: the same rows, each value made a number with `CAST` |
| **gold** | business-ready: shaped for the reports and the questions people ask, often aggregated | GDP per person, 2024: two silvers joined |

A convention, not a law: the names and the number of layers vary from team to team, and the idea (keep the raw data,
clean it once, shape it for the reader) is older than the names. A layer is not a guarantee: silver is only as
trustworthy as its checks, which is why this course counts between the layers. In a company each layer is stored as
a table or a file; in our notebooks each is a view.

**Supplied: bronze.** The setup reads the pages once into `gdp_bronze` and `population_bronze`, each with the columns
`country`, `year` and `value`, all text, as the API sent them. You read it; you never write it.

**Silver: the value made a number with `CAST`.** The same rows; the year and the value become numbers.

```sql
CREATE OR REPLACE VIEW gdp_silver AS
SELECT country,
       CAST(year AS INTEGER) AS year,
       CAST(value AS DOUBLE) AS gdp
FROM gdp_bronze;

CREATE OR REPLACE VIEW population_silver AS
SELECT country,
       CAST(year AS INTEGER) AS year,
       CAST(value AS BIGINT) AS population
FROM population_bronze;
```

**Gold: two silvers joined.** The `ON` line has two conditions: the same place and the same year (the World Bank's
names are unique in each year of these pages).

```sql
CREATE OR REPLACE VIEW gold AS
SELECT g.country, g.gdp / p.population AS gdp_per_person
FROM gdp_silver AS g
JOIN population_silver AS p
  ON p.country = g.country AND p.year = g.year
WHERE g.year = 2024 AND g.gdp IS NOT NULL;
```

**What you write: the count between the layers.** `COUNT(*)` counts rows; `COUNT` of a column counts the rows that
hold a value: a `NULL` is still a row. A count that drops shows a loss; counts that agree cannot rule out every loss,
so read the rows too.

```sql
SELECT COUNT(*) AS rows, COUNT(value) AS numbers FROM gdp_bronze;              -- 3975 and 3681
SELECT COUNT(*) AS rows, COUNT(gdp) AS numbers FROM gdp_silver;                -- 3975 and 3681

SELECT COUNT(*) - COUNT(gdp) AS no_number FROM gdp_silver WHERE year = 2024;   -- 23
```

**`CAST` against `TRY_CAST`.** `CAST` refuses a value it cannot read, and names it: population made an `INTEGER`
(which holds up to 2,147,483,647) stops at `'3552306404'`. **`TRY_CAST` never errors**: what it cannot read becomes
`NULL`, silently. World's population is one of them, so a gold built on that silver has no GDP per person for World,
and no error says so.

```sql
SELECT COUNT(TRY_CAST(value AS INTEGER)) AS numbers_in_silver
FROM population_bronze;                                                         -- 3810 of bronze's 3960
```

**A Eurostat file (Lab 6's).** The first column packs several codes, separated by commas; there is one column per
year; every cell is text: a number (`806`), a number and a flag after a space (`755 p`: provisional; `628 e`:
estimated), or `:` (not available). In its bronze a number is a cell that does not start with a colon:
`raw_value NOT LIKE ':%'`. `TRY_CAST` on a whole cell makes `NULL` of every number with a flag.

The first layer where a count drops, for no reason you can name, is the layer to repair.

## 9. Comparing two numbers

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

## 10. Asking an assistant about a query

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
