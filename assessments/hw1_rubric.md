---
title: "Homework 1 Rubric"
subtitle: "One table, the weekly numbers"
author: "ECBS5294 — Working with Data"
titlepage: false
toc: false
geometry: margin=1in
---

# Homework 1 — Rubric

**ECBS5294 — Working with Data**

**Deliverable:** Homework 1 — One table, the weekly numbers
**Format:** `hw1-submission.zip` (made with `git archive` from your commits, as `SUBMITTING.md` says), uploaded to Moodle
**Total points:** 100

## Overview

You answer seven questions from one table, the Online Retail II invoice lines, to the definitions in finance's brief
(questions 1 to 5, 7 and 8; questions 6 and 9 are stretch, and not scored). Every answer has five parts, in the
notebook: the sentence (what the number measures, which rows it comes from, which rows the query left out); the rows
you expect, with their source; the query and its number; the check, in the kind the question names (a sum, a ranked
list, a ratio); and one line on how you would know if the number were wrong. A reconciliation cell shows that revenue
by month, revenue by country, and the grand total agree to the penny.

This rubric grades the *reasons* at least as much as the numbers. Criterion 1 scores the numbers; criteria 2 and 3
score the other parts around them. A right number with no check and no "how would I know" line is a number that could
be right by accident, and it is scored that way.

## How scoring works

Every criterion is scored at **exactly one of its three anchor values** — no in-between points. Read each table from
the bottom up: a submission that meets **any** condition in the *Needs Improvement* row scores that; otherwise, one that
meets **every** condition in the *Excellent* row scores Excellent; everything else scores *Satisfactory*. So each
submission meets exactly one anchor. The rule picks the anchor; judging the evidence against each condition is still a
grader's call, and borderline submissions are scored by two graders.

## Rubric

### 1. Correct answers (30 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 30 | All of these: **6 or 7 of the 7** core numbers match the key — the number *and* its population: a top ten is the same ten products in the same order, money to the penny; and the notebook runs top to bottom after *Restart*, then *Run All*, from a fresh unzip. |
| **Satisfactory** | 20 | Neither of the other rows. For example: 5 numbers match; or the notebook runs from a fresh unzip only when its cells are run out of order. |
| **Needs Improvement** | 8 | Either of these: **4 or fewer** numbers match; or the notebook does not run from a fresh unzip in any order. |

**What we're looking for:** each number computed to the brief's sentence, not to a filter that happens to land close.
Two filters that sound alike can agree on revenue and disagree about two thousand lines; the key checks the population,
not only the total.

### 2. The checks, and the "how would I know if this were wrong" lines (30 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 30 | All of these: every core question has its check **in the kind its heading names** — a sum split into groups that add back up, a ranked list with its row count and its top row reached a second way, a ratio with its top and bottom checked separately on the same lines — with its output and one line saying what it shows, or, where no query would catch the error, how the number could be wrong and what you would see; the reconciliation cell prints revenue by month (added up), revenue by country (every country, added up) and the grand total, and shows they agree **to the penny**, `abs(a - b) < 0.005` or both rounded to the cent, never `==`; **and** every core question has a "how would I know if this were wrong" line that names the lines in the file that would change the number and the section 0 cell that shows them — a cause, not a restatement of the query. |
| **Satisfactory** | 20 | Neither of the other rows. For example: the reconciliation closes, but one or more checks is not in the kind the heading names, or is the answer's own query run twice; or every check is real, but the reconciliation is missing, covers fewer than all countries, or tests sums of money with `==`; or a "how would I know" line is missing, or restates the query ("I used `IS NULL`") instead of naming the lines. |
| **Needs Improvement** | 8 | Both of these: **4 or more of the 7** core questions have no check in the kind named (none, or one that restates the answer), **and** the reconciliation is missing or does not close. |

**What we're looking for:** a check that would have caught the mistake it is guarding against, in the shape the
question told you to use, and one honest line per question about where the file could have bitten. "For a line with
no description, `!= 'Manual'` is unknown, and `WHERE` keeps only true, so 2,928 lines would vanish; they are priced at
zero, so revenue would not move and only the line count shows it (`I6`)" is the line. "I used `IS NULL`" is not.

### 3. The sentences, and the rows you expected (25 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 25 | All of these: every core question has a sentence that says, in plain English, what the number measures, which rows it comes from, and which rows the query left out, and the query does what the sentence says and nothing more; every core question has its row estimate written before the query, **with its source** — "the brief fixes it", a named section 0 cell, or "neither fixes this" — and, where the file alone could fix it, one word after the query on whether the file agreed; question 1 states the grain, says what the key test showed, and reports the duplicate count as a measurement, not a repair. |
| **Satisfactory** | 17 | Neither of the other rows. For example: one or two sentences missing or not matching their query; an estimate with no source, or written after the query; a question 1 that stops at "not a key". |
| **Needs Improvement** | 6 | Either of these: **4 or more of the 7** core questions have no sentence; or the estimates are missing throughout. |

**What we're looking for:** the sentence is the definition the query has to meet, written before the query exists;
the estimate is the first thing that catches a wrong `GROUP BY` or a merged December. Both are cheap, and both catch
more than any later check.

### 4. Clean submission and Git (10 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 10 | All of these: `hw1-submission.zip` made with `git archive` contains everything `SUBMITTING.md` lists and nothing else (no `.venv/`); `GIT_LOG.txt` shows more than one commit, with messages that **name the cause** ("Count units on priced lines only: zero-priced lines are stock write-offs"); `data/raw/` is unchanged. |
| **Satisfactory** | 6 | Neither of the other rows. For example: commit messages are "fix" or "update", or there is one commit only. |
| **Needs Improvement** | 2 | Any of these: a file `SUBMITTING.md` lists is missing from the archive (`GIT_LOG.txt` included); `.venv/` is inside it; `data/raw/` is edited. |

**What we're looking for:** a history a colleague can read, and a zip a stranger can run.

### 5. AI use disclosure (5 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 5 | Specific, and consistent with the submission: what you asked, what the assistant got right or wrong, and which query **you** ran to check it, with what it returned. Or an honest "did not use AI." |
| **Satisfactory** | 3 | Present and consistent with the submission, but generic ("used it to help with SQL"). |
| **Needs Improvement** | 1 | Missing, or contradicted by the submission. |

**What we're looking for:** whether you checked the assistant's query against the data. An assistant writes SQL that
runs; it does not know this file's conventions unless you paste them.

## General Notes

- The checks, the "how would I know" lines, the sentences and the estimates together (55 points) outweigh the answers
  (30). A set of right numbers you cannot explain loses meaningful credit; a partly right set with honest reasons and
  real checks can still do well.
- Questions 6 and 9 are stretch. They are not scored: a stretch answer, right or wrong, with or without a check,
  changes no criterion.
- The brief is the definition. Where you believe a question and the file disagree, apply the brief and say in the
  sentence which clause you applied.
- Every number must come from a query on `data/raw/online_retail.parquet`, in a cell that ran in order. A number typed
  into a markdown cell with no query behind it scores as not answered.
- AI tools are allowed. You must be able to explain everything you submit, in your own words, without notes.
- Late: accepted up to one day late at −10%; nothing after Sunday 23:59 (syllabus, *Policies*).
