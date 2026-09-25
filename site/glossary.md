---
title: Glossary
layout: default
nav_order: 7
---
<!-- Owned by the TA team: expand freely (plain sentences, no jargon defining jargon).
     course check validates only the frontmatter. Keep entries alphabetical. -->

# Glossary

Every term this course uses, one or two plain sentences each. If a word on this site or in a lab confuses you and is
not here, tell a TA: that is a bug in this page, not in you. The words DS1 taught (terminal, kernel, `.venv`, `uv`,
commit, working directory) are in [DS1's glossary](https://earino.github.io/ecbs5293/site/glossary.html); the few
this course leans on hardest are repeated here.

**Aggregate (function)** — a function that turns many rows into one number: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`.

**Aggregate row** — a row that is a total of other rows in the same table: a region listed among its members, a
"total" line in a list of items. Summing a column that contains aggregate rows counts the same thing more than once.
The census is how you find them.

**Alias** — a name given to a column or a table with `AS`: `co2 * 1000000 / population AS tonnes_per_person`.
The name is born in `SELECT`, which runs after `WHERE`, so standard SQL does not let `WHERE` use it. DuckDB does, as a
shortcut; the course writes the expression again, so the query means the same in any database.

**Anti-join** — a query that finds the rows of one table with **no** match in another: a `LEFT JOIN` followed by
`WHERE right_table.key IS NULL`. It shows you what an `INNER JOIN` would have thrown away.

**API** — a web address that returns data instead of a page. You send a request (often with parameters in the
address) and get back a document, usually JSON.

**Assertion** — a line of code that stops the program if a condition is false: `assert duplicates == 0, f"{duplicates} duplicate keys"`. The course's checks are assertions.

**Base (of a rate)** — the currency a set of exchange rates is quoted against, stated in the response itself. It
decides which way every rate points: write the unit down (*… per …*) before you multiply or divide by it.

**Bronze** — the first layer of a pipeline: the data exactly as it arrived, never edited, with a record of where it
came from and when. In this course, `data/raw/`.

**Cache** — a saved copy of an API's answer, kept in the project (`data/raw/api/`) so the analysis never depends on
the network and always reads the same document.

**Census** — the count of every distinct value in a column: `SELECT col, COUNT(*) FROM t GROUP BY col ORDER BY 2 DESC`.
All of it, never the top few. It is the first thing you run on a column you have not seen.

**Check** — see *Validation*.

**Clause** — one part of a SQL query, starting with its keyword: `SELECT`, `FROM`, `WHERE`, `GROUP BY`, `HAVING`,
`ORDER BY`, `LIMIT`. The written queries in the knowledge checks and the exam are scored clause by clause.

**Composite key** — a key made of more than one column, such as `(country, year)`: no single column is unique, but
the combination is.

**`COUNT(*)` and `COUNT(column)`** — `COUNT(*)` counts rows; `COUNT(column)` counts the rows where that column has a
value. The difference between them is the number of `NULL`s.

**CTE (common table expression)** — a named step inside one query, written `WITH name AS (SELECT …)`. The course uses
it to aggregate a measure to its own grain before a join.

**Data dictionary** — a document that says, for every column of a table: its type, its meaning, its unit, its allowed
values, what a missing value means, and what cleaning did to it.

**Diagnosis note** — the five-part note every lab and homework ends with: symptom, cause, evidence, change,
verification. In the homeworks it includes a *trap log*.

**Dimension** — a column you group by or filter on (state, month, payment type), as opposed to a *measure*.

**Domain** — the set of values a column is allowed to hold: a list of statuses, a range of dates, "never negative".

**DuckDB** — the database this course uses. It runs inside Python with no server, and reads a CSV, Parquet, or JSON
file as a table with no loading step. Every project pins version 1.5.5.

**Fan-out** — what a join does when a row on one side matches several rows on the other: the row is repeated once
per match, and anything summed from it is counted several times. The query runs; the number is wrong.

**Fail fast** — stopping a pipeline at the first failed check, with the number in the message, instead of carrying
on and producing a wrong table.

**Flag** — a letter a statistics office attaches to a value to say something about it, such as *estimated* or
*provisional*. In a raw file it can be glued to the number: Eurostat's municipal waste file has `628 e`, 628 kg per
inhabitant, estimated. A flag is information: it is kept, in a column of its own.

**Foreign key** — a column that holds another table's key, such as `customer_id` in an orders table. It is what a
join matches on.

**Fresh-clone test** — cloning your project into a new folder, running `uv sync` and the pipeline, and checking that
it works. If it runs only on your laptop, it does not run.

**Gold** — the last layer of a pipeline: the answer tables, at the grain of the question, made by code from silver.

**Grain** — what one row of a table is: one invoice line, one country in one year, one order. Finish the sentence
*one row is one ___*, and prove it with the key test, before computing anything.

**`GROUP BY`** — the clause that collapses rows into one row per value (or combination of values) of the columns you
name, so that an aggregate can be computed per group.

**`HAVING`** — the clause that filters groups after `GROUP BY`, for example `HAVING COUNT(*) > 100`. `WHERE` filters
rows before grouping and cannot use an aggregate.

**Idempotent** — a step that gives the same result however many times you run it. A pipeline step rebuilds its table
from the layer below every time; it never adds to what the last run left.

**Inference (type inference)** — DuckDB guessing each column's type by reading its values. One stock code with a letter
in it (`85123A`) makes Online Retail's whole `StockCode` column text. `DESCRIBE` shows what it guessed.

**`INNER JOIN`** — a join that keeps only the rows that found a match on both sides. The unmatched rows disappear,
without an error.

**JSON** — a text format for documents made of lists `[…]` and key–value objects `{…}`, nested inside each other.
Most APIs answer in JSON.

**Key (primary key)** — the column, or set of columns, that identifies each row: unique, and never missing.
*Unique* is a claim you test (`COUNT(*)` against `COUNT(DISTINCT key)`), not a property you assume.

**Key test** — `SELECT COUNT(*), COUNT(DISTINCT key) FROM t`. If the two numbers differ, the key is not a key.

**Knowledge check** — a 10-minute closed-book check at the start of Sessions 2 and 3, on the session before.

**Lab checkpoint** — the Moodle slot where you upload your diagnosis note at the end of each lab, with a first line
saying who you explained it to and what you need help with. A lab is complete when it is in; labs carry no points.

**`LEFT JOIN`** — a join that keeps every row of the left table; where the right table has no match, its columns are
`NULL`.

**Long and wide** — a *wide* table has one column per year (or per month); a *long* table has one row per year, with
the year in a column. Analysis wants long. See *Unpivot*.

**Measure** — the number you sum, count, or average: revenue, tonnes, items. A measure has a grain of its own.

**Metric** — a measure with its definition written as a sentence: the filter, the measure, and the population it is
computed over. *Revenue is the sum of quantity times price over lines that are not …, by month.*

**Normalization** — splitting a document or a wide table into one table per grain, each with its own key, linked by
foreign keys.

**`NULL`** — SQL's marker for *no value*. Not zero, not an empty string, and not equal to anything, including
another `NULL`: `= NULL` is never true, `!=` drops `NULL` rows, `COUNT(column)` and `AVG` skip them, and a `NULL`
join key matches nothing.

**NULL-safe filter** — a condition that excludes one value while keeping the rows that have no value:
`col IS DISTINCT FROM 'x'`, or `col != 'x' OR col IS NULL`.

**Pagination** — an API sending its answer one page at a time. The response says how many pages and how many records
there are; reading page 1 alone is part of the truth, with no error.

**Parquet** — a file format for tables: the same rows as a CSV, stored by column, compressed, with the types saved.
DuckDB reads it directly.

**Pipeline** — a set of scripts that turns raw data into answer tables, in order, every time the same way. In this
course: `pipeline.py` runs `clean.py` (bronze to silver), `report.py` (silver to gold), and `checks.py`.

**`PROJECT_ROOT` anchor** — the first cell of every notebook, which finds the project folder and moves there, so that
`'data/raw/…'` means the same file wherever the notebook was opened from (DS1, Block 1).

**Quarantine** — setting an invalid row aside, in a rejects file with the reason, instead of either keeping it or
silently dropping it. The run continues, and the accounting counts it.

**Reconciliation** — checking a number against a second, independent computation, using the identity the number
permits: an additive measure's breakdown sums to its total; a ratio's numerator and denominator are checked
separately; an average recombines only with its weights.

**Relationship (one-to-one, one-to-many, many-to-many)** — how many rows of one table can match one row of another.
It decides what a join does to the row count.

**Residual** — what is left when two numbers that should agree are subtracted. A residual is reported as a number,
and explained as far as the documentation allows; the rest is reported as unexplained.

**Row-count discipline** — saying how many rows you expect before you run a query, and counting after every join and
every filter.

**Schema** — the list of a table's columns and their types. `DESCRIBE` prints it.

**Self-check key** — the worked answers to a homework, posted on Moodle after its late window closes, so you can
check your own work before the next knowledge check or the exam.

**Sentinel** — a value that stands in for "missing": `:`, `N/A`, `-999`, sometimes `0`. It has to be turned into
`NULL` on purpose, or it is counted as data.

**Silver** — the middle layer of a pipeline: typed, keyed, one row per stated grain, validated, and documented in a
data dictionary. Made by code from bronze.

**SQL** — the language of questions to tables. It is declarative: you write *what* you want, and the database decides
how to get it, which is why a colleague can read your question in the query.

**Stop (a run)** — what a pipeline does when a rule that must always hold is broken: a duplicate key, a total that
does not add up. Compare *Quarantine*, which is for a single bad row.

**Three counts** — after every join: the rows in the left table, the rows in the result, and the distinct left keys
in the result. Together they say whether the join multiplied rows or dropped them.

**Tidy** — a table where every column is one variable, every row is one observation, and every cell is one value.

**Tolerance** — how close two numbers must be to count as agreeing: to the cent for money, to the source's rounding
for tonnes and people.

**Trap log** — in a homework's diagnosis note, one entry for each problem in the data that changed a number, even if
your own query was never wrong.

**`TRY_CAST`** — converts a value to another type and returns `NULL`, silently, when it cannot. Every `TRY_CAST` needs
a count of what it turned into nothing.

**Type** — what kind of value a column holds: integer, decimal number, text (`VARCHAR`), date, timestamp. The type
decides what you can do with it: DuckDB refuses to add text.

**Unit** — what a number is measured in: tonnes, million tonnes, kilograms per inhabitant, euros. It lives in the
documentation, not in the column. A table has one unit per measure, or its sums mean nothing.

**Unpivot** — turning columns into rows: a wide table with a column per year becomes a long table with one row per
year. DuckDB's `UNPIVOT` does it.

**Validation** — a check written as code that can fail: a query whose answer must be a known number, and an assertion
that stops the run if it is not. Four families: **key** (unique, never missing), **domain** (allowed values and
ranges), **count** (rows in = rows out + rows rejected), **reconciliation** (two computations agree).

**Vacuous check** — a check that cannot fail on the data it is meant to catch: `assert n > 0`, a total compared with
itself. Ask of every check: *what wrong data would this pass?*

**View** — a saved query with a name (`CREATE VIEW real_orders AS SELECT …`). Write a filter once, as a view, and read
from it everywhere after.

**Weighted mean** — an average that counts each part by its size. Averages by group recombine into the overall
average only as `SUM(n × mean) / SUM(n)`; the plain average of the averages is a different number.
