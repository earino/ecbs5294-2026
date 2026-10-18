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

**Anti-join** — the usual name for the query the course calls *the rows with no partner*: a `LEFT JOIN` followed by
`WHERE right_table.key IS NULL`. It finds the rows of one table that match nothing in another, which are the rows a
plain `JOIN` leaves out.

**API** — a web address that returns data instead of a page. You send a request (often with parameters in the
address) and get back a document, usually JSON.

**Assertion** — a line of code that stops the program if a condition is false: `assert duplicates == 0, f"{duplicates} duplicate keys"`. The course's notebook checks print `PASS` or `FAIL` instead, for you to read.

**Base (of a rate)** — the currency a set of exchange rates is quoted against, stated in the response itself. It
decides which way every rate points: write the unit down (*… per …*) before you multiply or divide by it.

**Bronze** — the first layer: the data exactly as it arrived, never edited, so that every later table can be rebuilt
from it (Block 6). In this course, the files in `data/raw/`, and the views made straight from them.

**Cache** — a saved copy of an API's answer, kept in the project (`data/raw/api/`) so the analysis never depends on
the network and always reads the same document.

**Census** — the count of every distinct value in a column: `SELECT col, COUNT(*) FROM t GROUP BY col ORDER BY 2 DESC`.
All of it, never the top few. It is the first thing you run on a column you have not seen.

**Check** — see *Validation*.

**Clause** — one part of a SQL query, starting with its keyword: `SELECT`, `FROM`, `WHERE`, `GROUP BY`, `HAVING`,
`ORDER BY`, `LIMIT`. The written queries in the knowledge checks and the exam are scored clause by clause.

**Composite key** — a key made of more than one column, such as `(country, year)`: no single column is unique, but
the combination is.

**Country** — in this course, the file's word, not a political one. A `country` or `geo` column holds whatever its
publisher reports there: states, territories, places with no government at all (Antarctica), and often groups (`World`,
the EU). When a lab says *country*, it means a row for one place, as opposed to a group of places. The word says
nothing about what any place is politically, and no question in this course asks.

**`COUNT(*)` and `COUNT(column)`** — `COUNT(*)` counts rows; `COUNT(column)` counts the rows where that column has a
value. The difference between them is the number of `NULL`s.

**CTE (common table expression)** — a named step inside one query, written `WITH name AS (SELECT …)`. The course uses
it to aggregate a measure to its own grain before a join.

**Data dictionary** — a document that says, for every column of a table: its type, its meaning, its unit, its allowed
values, what a missing value means, and what cleaning did to it.

**Diagnosis note** — the note Session 1's labs ended with, in five parts: symptom, cause, evidence, change,
verification. From Session 2 there is no note: the cause is one comment in the notebook's repair cell, and the checks
print the evidence. The final exam asks for one. In the homeworks the reasoning lives in the notebook: see *The four pieces*.

**Dimension** — a column you group by or filter on (state, month, payment type), as opposed to a *measure*.

**Domain** — the set of values a column is allowed to hold: a list of statuses, a range of dates, "never negative".

**DuckDB** — the database this course uses. It runs inside Python with no server, and reads a CSV, Parquet, or JSON
file as a table with no loading step. Every project pins version 1.5.5.

**Fan-out** — the usual name for what a join does when a row on one side has several partners on the other: the row
is repeated once per partner, so one row of the result is no longer one row of that table. Count or average the
result as if it were, and the query runs and the number is wrong. Block 4's repair: one row per order first, then
join.

**Fail fast** — stopping at the first failed check, with the number in the message, instead of carrying on and
producing a wrong table. `CAST` fails fast; `TRY_CAST` does not.

**Flag** — a letter a statistics office attaches to a value to say something about it, such as *estimated* or
*provisional*. In a raw file it can be glued to the number: Eurostat's municipal waste file has `628 e`, 628 kg per
inhabitant, estimated. A flag is information: it is kept, in a column of its own.

**Foreign key** — a column that holds another table's key, such as `customer_id` in an orders table. It is what a
join matches on.

**Fresh-clone test** — cloning your project into a new folder, running `uv sync`, and running the notebook from the
top: *Restart*, then *Run All*. If it runs only on your laptop, it does not run.

**Gold** — the last layer: business-ready, shaped for the reports and the questions people ask, often summed or
counted by group (Block 6). In Block 6, one table for one briefing.

**Grain** — what one row of a table is: one invoice line, one country in one year, one order. Finish the sentence
*one row is one ___*, and prove it with the key test, before computing anything.

**`GROUP BY`** — the clause that collapses rows into one row per value (or combination of values) of the columns you
name, so that an aggregate can be computed per group.

**`HAVING`** — the clause that filters groups after `GROUP BY`, for example `HAVING COUNT(*) > 100`. `WHERE` filters
rows before grouping and cannot use an aggregate.

**Idempotent** — a step that gives the same result however many times you run it. Each layer is made again from the
layer below every time; it never adds to what the last run left.

**Inference (type inference)** — DuckDB guessing each column's type by reading its values. One stock code with a letter
in it (`85123A`) makes Online Retail's whole `StockCode` column text. `DESCRIBE` shows what it guessed.

**`INNER JOIN`** — a join that keeps only the rows that found a match on both sides. The unmatched rows disappear,
without an error.

**JSON** — a text format for documents made of lists `[…]` and key–value objects `{…}`, nested inside each other.
Most APIs answer in JSON.

**Key (primary key)** — the column, or set of columns, that identifies each row: unique, and never missing.
*Unique* is a claim you test (`COUNT(*)` against `COUNT(DISTINCT key)`), not a property you assume.

**Key test** — `SELECT COUNT(*), COUNT(DISTINCT key) FROM t`. If the two numbers differ, the key is not a key.

**Knowledge check** — a 10-minute closed-book check at the start of Sessions 2 and 3, on the session before. One A4 sheet of notes, handwritten by you, both sides, is allowed.

**Lab checkpoint** — the Moodle slot a lab ends with. In Session 1 you uploaded your diagnosis note. From Session 2
the team's notebook is uploaded by the keyboard, and everyone else pastes the team's names and roles as their
submission text. A lab is complete when it is in; labs carry no points.

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

**Partner** — the row a join matches a row with. Every order has exactly one partner in `customers`; an order can
have several partners in `order_items`, or none. `JOIN` keeps only the rows that found a partner; `LEFT JOIN` keeps
every row of the left table.

**Pagination** — an API sending its answer one page at a time. The response says how many pages and how many records
there are; reading page 1 alone is part of the truth, with no error.

**Parquet** — a file format for tables: the same rows as a CSV, stored by column, compressed, with the types saved.
DuckDB reads it directly.

**Pipeline** — the steps that turn raw data into answer tables, in order, every time the same way. In this course,
the cells of a notebook: bronze, then silver, then gold, each made by code from the layer before, never by hand.

**`PROJECT_ROOT` anchor** — the first cell of every notebook, which finds the project folder and moves there, so that
`'data/raw/…'` means the same file wherever the notebook was opened from (DS1, Block 1).

**Quarantine** — setting an invalid row aside, with the reason, instead of either keeping it or silently dropping it.
Not taught this year; the word appears in older notes.

**Reconciliation** — checking a number against a second, independent computation, using the identity the number
permits: an additive measure's breakdown sums to its total; a ratio's numerator and denominator are checked
separately; an average recombines only with its weights.

**Relationship (one-to-one, one-to-many, many-to-many)** — how many rows of one table can match one row of another.
It decides what a join does to the row count.

**Residual** — what is left when two numbers that should agree are subtracted. A residual is reported as a number,
and explained as far as the documentation allows; the rest is reported as unexplained.

**Row-count discipline** — saying how many rows a query should return before you run it, and counting after every join and
every filter.

**Schema** — the list of a table's columns and their types. `DESCRIBE` prints it.

**Self-check key** — the worked answers to a homework, posted on Moodle after its late window closes, so you can
check your own work before the next knowledge check or the exam.

**Sentinel** — a value that stands in for "missing": `:`, `N/A`, `-999`, sometimes `0`. It has to be turned into
`NULL` on purpose, or it is counted as data.

**Silver** — the middle layer: cleaned and checked. The right types, consistent and explicit units, duplicates and
missing values handled, sometimes joined with other data; still at the detail of the records, not summed (Block 6). In this course, also: the flags kept, and every
number bronze had. A layer is only as good as its checks.

**SQL** — the language of questions to tables. It is declarative: you write *what* you want, and the database decides
how to get it, which is why a colleague can read your question in the query.

**Stop (a run)** — what code should do when a rule that must always hold is broken: a duplicate key, a total that
does not add up, a cell `CAST` cannot read.

**Three counts** — after every join: the rows that went in, the rows that came out, and the distinct keys that came
out. Together they say whether the join repeated rows or left some out, and what one row of the result is. With
`JOIN`, all three equal means every row found exactly one partner; with `LEFT JOIN` it means only that every left
row is there once.

**Tidy** — a table where every column is one variable, every row is one observation, and every cell is one value.

**Tolerance** — how close two numbers must be to count as agreeing: to the cent for money, to the source's rounding
for tonnes and people.

**The four pieces** — how a Homework 2 question is answered, inside the notebook: the query, what it printed, the
supplied check cell filled in, and one sentence (what the number measures, which rows it comes from, which rows the
query left out). Homework 1 asked for five written parts and kept them; Homework 3 adds a count you write yourself under each question.

**Three kinds of check** — a sum splits into groups that add back up; a ranked list gets its row count and its top
row reached a second way; a ratio gets its top and its bottom checked separately, on the same lines.

**`TRY_CAST`** — converts a value to another type and returns `NULL`, silently, when it cannot. Every `TRY_CAST` needs
a count of what it turned into nothing.

**Type** — what kind of value a column holds: integer, decimal number, text (`VARCHAR`), date, timestamp. The type
decides what you can do with it: DuckDB refuses to add text.

**Unit** — what a number is measured in: tonnes, million tonnes, kilograms per inhabitant, euros. It lives in the
documentation, not in the column. A table has one unit per measure, or its sums mean nothing.

**Unpivot** — turning columns into rows: a wide table with a column per year becomes a long table with one row per
year. DuckDB's `UNPIVOT` does it.

**Validation** — a check written as code that can fail: a query whose answer must be a known number, compared with
it. In the labs and homeworks it prints `PASS` or `FAIL` first. The course's checks are mostly counts: the key test,
the three counts of a join, a count against what a document promises, and the count between two layers.

**Vacuous check** — a check that cannot fail on the data it is meant to catch: `assert n > 0`, a total compared with
itself. Ask of every check: *what wrong data would this pass?*

**View** — a saved query with a name (`CREATE VIEW real_orders AS SELECT …`). Write a filter once, as a view, and read
from it everywhere after.

**Weighted mean** — an average that counts each part by its size. Averages by group recombine into the overall
average only as `SUM(n × mean) / SUM(n)`; the plain average of the averages is a different number.
