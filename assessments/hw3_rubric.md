---
title: "Homework 3 Rubric"
subtitle: "Waste per person: two files, three layers"
author: "ECBS5294 — Working with Data"
titlepage: false
toc: false
geometry: margin=1in
---

# Homework 3 — Rubric

**ECBS5294 — Working with Data**

**Deliverable:** Homework 3 — Waste per person: two files, three layers
**Format:** `notebooks/report.ipynb`, run top to bottom with its outputs saved, uploaded to Moodle. The notebook is the whole submission.
**Total points:** 100

## Overview

You answer one question, alone: municipal waste generated per person for the 27 EU members, in 2024, from
Eurostat's waste file and the World Bank's population pages. The setup turns both into tables; **every query is
yours**, from an empty cell. Four questions: did every record arrive (the rows of `population` against the `total` the
pages state, and the members with a 2024 population); did every value survive (`waste_silver`, made with `CAST`, the
numbers before and after, the numbers year by year, and the members with no 2024 number); gold, `per_person_2024`,
joined through `members`, shown highest first, after your prediction of its rows and numbers; and a colleague's
version of the gold, which runs with no error and loses a member (`missing_from_report`, then
`per_person_repaired`). Under each question: your queries, **your own count**, the supplied check, run (questions 2
to 4), and one sentence. Then three lines of reflection on the course, and the AI line.

Criterion 1 scores the answers, criterion 2 your counts and the checks, criterion 3 the sentences, criterion 4 the
reflection.

## How scoring works

Each criterion has **three possible scores**, and nothing in between. Read each table from the bottom up. If a
submission meets **any** condition in the *Needs Improvement* row, it gets that score. If not, and it meets **every**
condition in the *Excellent* row, it gets Excellent. Otherwise it gets *Satisfactory*. The grader judges the evidence
for each condition, and two graders score a borderline submission.

The grader runs the submitted notebook inside an untouched copy of the starter (*Restart*, *Run All*) and grades what
it prints there; the saved outputs are read beside it. A notebook whose saved outputs disagree with what it prints
when run is graded on what it prints.

## Rubric

### 1. Correct answers to the four questions (45 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 45 | All of these: **all four** answers match the brief: question 1 states the rows of `population`, the `total` the pages state and the members with a 2024 population, each from a query; question 2's `waste_silver` holds every place and year of the file's totals in thousand tonnes, with every number the file has, as a number, and its queries show the numbers per year and name the members with no 2024 number; question 3's `per_person_2024` has one row per member, 27 rows, with waste per person in kilograms for 2024, a number for each member with a 2024 total and `NULL` for each member without one, shown highest first; question 4's `missing_from_report` lists the member the colleague's table has no row for, and `per_person_repaired` has one row per member with the right numbers; each view is saved under the name the question gives; and the notebook runs top to bottom after *Restart*, then *Run All*, inside a clean starter. |
| **Satisfactory** | 30 | Neither of the other rows. For example: **two or three** of the four answers match (questions 1 to 3 right and question 4 unfinished is here, not lower); or gold has the right rows but its numbers are in another unit (tonnes) or another year; or a gold that drops the members with no total, or gives them 0 in place of `NULL`; or a silver that turns some of the file's numbers into `NULL`, or keeps only the members (not wrong for gold, but not what the brief asks); or a repair that keeps the member's row with no number; or the notebook runs only when its cells are run out of order. |
| **Needs Improvement** | 14 | **One or no** answer matches. (A notebook that stops with an error is graded question by question on the cells that run; an unfinished attempt left as comments costs only its own question; a question 3 query that would be right on a right silver counts as matching, even when the silver above it is wrong.) |

**What we're looking for:** each layer made by code from the layer before, never by hand, with nothing lost on the way.
The supplied checks are necessary, not the definition: a view can get `PASS` on every line and still not be the table
the brief asks for.

### 2. Your counts, and the checks (25 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 25 | All of these: under every question, **your own count** is a query that runs and that counts what the question asks: in question 1 the rows of `population` beside the `total` the pages state, and the members with a 2024 population; in question 2 the numbers before your step (`silver_split`, the same filters) beside the numbers after it (`waste_silver`); in question 3 the rows of `per_person_2024` beside the rows with a number; in question 4 the rows of the colleague's table and its rows with a number; the supplied checks run; and the cell *The checks*, and the line that calls a check, are unchanged. (A count that rightly shows your own table is wrong scores here; the wrong table scores under criterion 1.) |
| **Satisfactory** | 17 | Neither of the other rows. For example: one count missing; a count that compares your view with itself (it cannot disagree with itself); a count whose two sides cover different rows (for example, one year before the step and every year after it). |
| **Needs Improvement** | 7 | Either of these: **two or more** of the four questions have no count; or a supplied check was edited so that it passes. (The grader runs the original checks; an accidental edit that changes nothing costs nothing.) |

**What we're looking for:** a count that would have caught the mistake it guards against: your result beside an
expectation from the source (the pages' `total`, the numbers before your step, the 27 members). It is graded from its
code and what it prints.

### 3. The prediction and the sentences (10 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 10 | The prediction above question 3 has two numbers and a reason, written before the gold (a wrong prediction costs nothing). Every question has its sentence, in plain English, matching the table under it. Question 1: whether every record arrived, and how the counts show it. Question 2: whether every value survived, how the count shows it, and the year with the fewest numbers. Question 3: the prediction against the count, and why the rows are what they are (the members with no 2024 total keep a row, with `NULL`); the highest and the lowest member with a number. Question 4: which number in the report is wrong, how the count shows it, and why (the join on the name found no partner for one member). |
| **Satisfactory** | 7 | Neither of the other rows. For example: no prediction, or one with no reason; one sentence that restates the question instead of explaining the count; a sentence that names the method ("I used `CAST`") instead of the rows. |
| **Needs Improvement** | 3 | **Two or more** of the four questions have no sentence, or a sentence that contradicts the table under it. |

**What we're looking for:** that the number was understood (its unit, its rows, what it leaves out: the members
with no total, the member a join lost), and that you looked at the answer, not only at the checks.

### 4. The end-of-course reflection (15 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 15 | The reflection cell answers all three prompts with **specifics from your own work**: a kind of silent failure, named with the lab or homework where you met it; a count from this homework and what mistake it would catch; a habit you will keep. Length is not scored. |
| **Satisfactory** | 10 | Neither of the other rows. For example: all three answered, one or two in general terms ("I learned to check my work") with no lab, homework or check named. |
| **Needs Improvement** | 4 | Missing, or two or more prompts unanswered, or every answer general. |

**What we're looking for:** that you can name, from your own work, where a number went wrong without an error, and
what you now do about it.

### 5. AI use (5 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 5 | The notebook's *AI use* cell has one line, consistent with the submission: what you asked, and which cell checked its answer. Or "did not use AI", accepted unless the submission contradicts it. |
| **Satisfactory** | 3 | Present and consistent with the submission, but it names no cell ("used it to help with SQL"). |
| **Needs Improvement** | 1 | Missing, or contradicted by the submission. |

**What we're looking for:** whether you checked the assistant's queries against the data. An assistant writes a
cast that runs; it cannot see what the cast swallowed unless you count.

## General Notes

- The answers carry 45 points and the counts 25. A partly right set with real counts and honest sentences can still
  do well.
- The brief is the definition. Where you believe a question and the files disagree, apply the brief and say in the
  sentence which clause you applied.
- Every number must come from a query on the files in `data/raw/`, in a cell that ran in order. A number typed into
  a markdown cell with no query behind it scores as not answered.
- `data/raw/` is never edited; a notebook that only runs on edited files does not run in a clean starter.
- AI tools are allowed. You must be able to explain every line of SQL you wrote or changed, in your own words, without
  notes. The supplied setup and checks are there to read if you want; nobody asks you to explain them.
- Late: accepted up to one day late at −10%; nothing after Sunday 23:59 (syllabus, *Policies*).
