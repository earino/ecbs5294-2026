---
title: "Homework 2 Rubric"
subtitle: "Tables that meet: three numbers for the board"
author: "ECBS5294 — Working with Data"
titlepage: false
toc: false
geometry: margin=1in
---

# Homework 2 — Rubric

**ECBS5294 — Working with Data**

**Deliverable:** Homework 2 — Tables that meet: three numbers for the board
**Format:** `notebooks/report.ipynb`, run top to bottom with its outputs saved, uploaded to Moodle. The notebook is the whole submission.
**Total points:** 100

## Overview

You answer three questions from four of Olist's tables, to the definitions in finance's brief: revenue by month; the
orders that have no review, by order status; and paid orders, revenue and average order value by customer state.
Each needs a join, and each join can leave rows out or repeat them without a word. Under every question the answer
is four things, in the notebook: the query, saved as the view the question names; what it printed; the supplied check
cell, with its blanks filled in and run (the blanks are the three counts of the join and the number computed without
the join); and one sentence on what the number measures, which rows it comes from, and which rows the query left
out. Nothing is written anywhere else.

Criterion 1 scores the answers, criterion 2 the checks, and criterion 3 the sentences. A right
number whose check is empty, or whose sentence says it counts something else, is a number that could be right by
accident, and it is scored that way.

## How scoring works

Each criterion has **three possible scores**, and nothing in between. Read each table from the bottom up. If a
submission meets **any** condition in the *Needs Improvement* row, it gets that score. If not, and it meets **every**
condition in the *Excellent* row, it gets Excellent. Otherwise it gets *Satisfactory*. The grader judges the evidence
for each condition, and two graders score a borderline submission.

The grader runs the submitted notebook inside an untouched copy of the starter (*Restart*, *Run All*) and grades what
it prints there; the saved outputs are read beside it. A notebook whose saved outputs disagree with what it prints
when run is graded on what it prints.

## Rubric

### 1. Correct answers to the three questions (50 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 50 | All of these: **all three** answers match the brief — the number *and* its rows: revenue by month has one row per month in which a paid order was placed, and the months add up to revenue; the orders with no review are the orders that have no review row, counted once each, by status; the state table counts **paid orders**, each once, with their revenue and the average order value as the brief defines it, money to the cent; each answer is saved as the view the question names and shown; and the notebook runs top to bottom after *Restart*, then *Run All*, inside a clean starter. |
| **Satisfactory** | 33 | Neither of the other rows. For example: **two** of the three answers match; or all three numbers are right but one table counts something the question did not ask for (payment rows called orders, months merged across years, an order with no payment counted as paid), or has rows the question did not ask for (a month in which no paid order was placed; more than one row for a status or a state); or the notebook runs only when its cells are run out of order. |
| **Needs Improvement** | 15 | Either of these: **one or none** of the three answers matches; or the notebook does not run in a clean starter in any order. |

**What we're looking for:** each number computed to the brief's sentence, from the table whose rows the sentence
counts. Correct means the brief. The lines supplied in the check cells are necessary, but they are not the
definition: a table can get `PASS` on every numbered line and still not be the table the brief asks for. A join that returns rows has not
thereby returned the right rows: an order paid in two parts is two payment rows, and an order with no review is in no
row of a plain join. A correct table made another way gets these marks too: for example, a count with `COUNT(DISTINCT …)`
after the rows were repeated, when the average is also computed as money divided by the distinct orders.

### 2. The checks, with the three counts for every join (30 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 30 | All of these: every question's check cell is filled in and runs, with **the three counts of its join** — the rows that went in, the rows that came out, the distinct keys that came out — as queries on the join the check names, and **the same number computed without that join** as a query on the source tables; the three counts count the key of the table the question's rows come from (payment rows, `(order_id, payment_sequential)`, for question 1; orders for questions 2 and 3); every numbered line the check cell prints starts with `PASS`; and the cell *The checks*, and the line that calls a check, are unchanged. The join each check names: question 1, `order_payments JOIN orders`; question 2, `orders LEFT JOIN order_reviews`; question 3, the one-row-per-order table joined to `orders` and `customers`. A check uses that join even when the answer took another route. |
| **Satisfactory** | 20 | Neither of the other rows. For example: the three counts are missing for one join, or count the wrong key (review rows called orders); a "second way" that reruns the answer's own join instead of the source tables; one line that prints `FAIL` in the submitted notebook; a supplied line edited so that it passes. |
| **Needs Improvement** | 8 | Either of these: **two or more** questions have no working check cell, or no three counts for their join; or a supplied line was deleted. |

**What we're looking for:** a check that would have caught the mistake it is guarding against, computed from the
files and not from the answer. The check is graded from its code and from what it prints, not from prose about it:
there is no prose to grade.

### 3. The sentences (15 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 15 | All of these: every question has one sentence that says, in plain English, what the number measures, which rows it comes from, and which rows the query left out, in the brief's words; the query does what the sentence says and nothing more; and section 0's three inspection cells (`I2`, `I3`, `I4`) are written, each with a comment that says what one row of the table is. |
| **Satisfactory** | 10 | Neither of the other rows. For example: one sentence that restates the question instead of naming the rows; a sentence that names the fix ("I used a `WITH`") instead of the rows; a section 0 cell missing, or with no comment on what one row is. |
| **Needs Improvement** | 4 | **Two or more** questions have no sentence, or a sentence that contradicts the table under it (says "orders" of a table whose rows are payments). |

**What we're looking for:** that the number was understood. A sentence that names the rows left out is the one that
catches a join that kept too few, and saying what one row is, before the join, is what catches a count that grew.

### 4. AI use (5 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 5 | The notebook's *AI use* cell has one line, consistent with the submission: what you asked, and which cell checked its answer (for a join, the cell with its three counts). Or "did not use AI", accepted unless the submission contradicts it. |
| **Satisfactory** | 3 | Present and consistent with the submission, but it names no cell ("used it to help with SQL"). |
| **Needs Improvement** | 1 | Missing, or contradicted by the submission. |

**What we're looking for:** whether you checked the assistant's joins against the data. An assistant writes a join
that runs; it cannot see that an order has two payment rows unless you tell it what one row is.

## General Notes

- The answers carry 50 points and the checks 30. A partly right set with real checks and honest sentences can still
  do well.
- The stretch is not scored. A stretch answer, right or wrong, changes no criterion. An error in the stretch cell
  does not count against "runs top to bottom": the grader runs the notebook with the stretch cell skipped. Still,
  if you do not finish it, leave it as comments.
- The brief is the definition. Where you believe a question and the files disagree, apply the brief and say in the
  sentence which clause you applied.
- Every number must come from a query on the files in `data/raw/`, in a cell that ran in order. A number typed into
  a markdown cell with no query behind it scores as not answered.
- `data/raw/` is never edited; a notebook that only runs on edited files does not run in a clean starter.
- AI tools are allowed. You must be able to explain everything you submit, in your own words, without notes.
- Late: accepted up to one day late at −10%; nothing after Sunday 23:59 (syllabus, *Policies*).
