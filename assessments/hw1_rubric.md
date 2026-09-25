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
**Format:** `hw1-submission.zip` (made with `git archive` from your commits, as `SUBMITTING.md` says) and the video, both uploaded to Moodle
**Total points:** 100

## Overview

You answer seven questions from one table, the Online Retail II invoice lines, to the definitions in finance's brief
(questions 1 to 5, 7 and 8; questions 6 and 9 are stretch, and not scored).
Each answer is a sentence, a query, a number, and a check. Every data issue that changed a number goes in the trap log
in `DIAGNOSIS.md`, whether or not your own query was ever wrong: five parts for a trap you fell into, four lines for one
you avoided, citing the notebook's cells by ID instead of pasting them again. A reconciliation cell shows that
revenue by month, revenue by country, and the grand total agree to the penny.

This rubric grades the *reasons* at least as much as the numbers. Criterion 1 scores the numbers; criterion 3 scores
whether the trap log shows the data issue behind each one was found. A missing trap-log entry costs marks once, in
criterion 3, never in both.

## How scoring works

Every criterion is scored at **exactly one of its three anchor values** — no in-between points. Read each table from
the bottom up: a submission that meets **any** condition in the *Needs Improvement* row scores that; otherwise, one that
meets **every** condition in the *Excellent* row scores Excellent; everything else scores *Satisfactory*. So each
submission meets exactly one anchor. The rule picks the anchor; judging the evidence against each condition is still a
grader's call, and borderline submissions are scored by two graders. The video is scored
per element.

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

### 2. Verification (20 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 20 | All of these: every core question has a check that **could fail** — an identity that must hold, a count that must be zero, or the same number reached another way — with its output and one line saying what it shows; the row count is written before each query; **and** the reconciliation cell prints revenue by month (added up), revenue by country (every country, added up), and the grand total, and shows they agree **to the penny** — `abs(a - b) < 0.005`, or both rounded to the cent, never `==`. |
| **Satisfactory** | 13 | Neither of the other rows. For example: the reconciliation closes, but one or more questions have no check that could fail (the answer's own query run twice is not a check); or every question has a real check, but the reconciliation is missing, covers fewer than all countries, or tests sums of money with `==`; or a row count is missing before a query. |
| **Needs Improvement** | 5 | Both of these: **4 or more of the 7** core questions have no check that could fail (none, or one that restates the answer), **and** the reconciliation is missing or does not close. |

**What we're looking for:** a check that would have caught the mistake it is guarding against. Revenue is additive, so
its breakdowns must add up to its total; an average is a ratio, so its top and its bottom are reconciled separately, on
the same lines. Be exact about what a check proves: three computations that agree show they agree, not that they
count the right lines — the census and the brief do that.

### 3. Diagnosis note: the trap log (20 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 20 | All of these: an entry for **every** data issue that changed one of the seven core numbers (the zero prices and the negative quantities may share one); a trap your query fell into has all five parts — symptom (the number, and what it was compared to), cause, evidence, change, verification — with the evidence from **before** the repair pasted, or cited by an inspection cell's ID with the line that matters, and the change and the verification citing the notebook's cells by ID (`Q8`, `Q8 check`, `R`); a trap your query never fell into has its four lines, including the number it *would* have changed; every cause is a cause, not a restatement of the change; every cited ID exists and shows what the entry says. |
| **Satisfactory** | 13 | Neither of the other rows. For example: at least five issues logged, but one or more missing; a part missing from an entry; evidence described rather than pasted or cited; an avoided trap with no number it would have changed; a cited ID that does not exist, or does not show what the entry says; a cause that restates the change ("I used `IS DISTINCT FROM`"). |
| **Needs Improvement** | 5 | Either of these: **4 or fewer** issues logged; or evidence you did not collect — output that no query in the notebook or the log produces. |

**What we're looking for:** "for a line with no description, `!= 'Manual'` is unknown, and `WHERE` keeps only true,
so 2,928 lines vanished; they are priced at zero, so revenue did not move and only the line count showed it", with
`Q8 check` cited for the verification — not "I fixed the Manual filter." Pasting the same query into the notebook and
the log earns nothing: cite the cell. Paste only what the notebook no longer shows.

### 4. Video walkthrough (15 points)

Scored per element: **full**, **half** (vague or wrong), or **0** (absent). The video takes **one trap** that changed
a number.

| Element | Full | Half | What earns full |
|---|---|---|---|
| Symptom | 3 | 1 | The number you first got, and what showed it was wrong (a check, a census, the brief) |
| Cause | 5 | 2 | Which lines, and why the query counted them — not just what you changed |
| Change | 3 | 1 | The query as it is now, on screen |
| Verification | 4 | 2 | *Restart* then *Run All* shown finishing, and the check with the two numbers that now agree |

Your voice is required: a silent recording scores 0 on all four elements — the point is you explaining. Open by naming
the homework and the repo; a missed name is a clean-submission matter (criterion 5), not a video one. 60–90 seconds.
Excellent / Satisfactory / Needs Improvement map to 13–15 / 8–12 / 0–7.

*Approved alternative formats* (see the syllabus's accessibility section — arrange in advance): a written walkthrough
with annotated screenshots, or an audio-only recording, is scored on the same four elements at the same values. The
"your voice" requirement applies to the standard video route only, never to an arranged accommodation.

### 5. Clean submission and Git (10 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 10 | All of these: `hw1-submission.zip` made with `git archive` contains everything `SUBMITTING.md` lists and nothing else (no `.venv/`); `README.md` has a *How to run this* section that works from a fresh unzip; `GIT_LOG.txt` shows more than one commit, with messages that **name the cause** ("Count units on priced lines only: zero-priced lines are stock write-offs"); `data/raw/` is unchanged; the video opens by naming the homework and the repo. |
| **Satisfactory** | 6 | Neither of the other rows. For example: commit messages are "fix" or "update", or there is one commit only; *How to run this* is missing or does not work; the video does not open by naming the homework and repo. |
| **Needs Improvement** | 2 | Any of these: a file `SUBMITTING.md` lists is missing from the archive (`GIT_LOG.txt` included); `.venv/` is inside it; `data/raw/` is edited. |

**What we're looking for:** a history a colleague can read, and a zip a stranger can run.

### 6. AI use disclosure (5 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 5 | Specific, and consistent with the submission: what you asked, what the assistant got right or wrong, and which query **you** ran to check it, with what it returned. Or an honest "did not use AI." |
| **Satisfactory** | 3 | Present and consistent with the submission, but generic ("used it to help with SQL"). |
| **Needs Improvement** | 1 | Missing, or contradicted by the submission. |

**What we're looking for:** whether you checked the assistant's query against the data. An assistant writes SQL that
runs; it does not know this file's conventions unless you paste them.

## General Notes

- The trap log, the verification, and the video together (55 points) outweigh the answers (30). A set of right numbers
  you cannot explain loses meaningful credit; a partly right set with honest traps and real checks can still do well.
- Questions 6 and 9 are stretch. They are not scored: a stretch answer, right or wrong, with or without a check or a
  trap entry, changes no criterion.
- The brief is the definition. Where you believe a question and the file disagree, apply the brief and say in the
  sentence which clause you applied.
- Every number must come from a query on `data/raw/online_retail.parquet`, in a cell that ran in order. A number typed
  into a markdown cell with no query behind it scores as not answered.
- AI tools are allowed. You must be able to explain everything you submit, in your own words, without notes.
- Late: accepted up to one day late at −10%; nothing after Saturday 23:59 (syllabus, *Policies*).
