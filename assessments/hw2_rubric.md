---
title: "Homework 2 Rubric"
subtitle: "Many tables and an API: the board-deck numbers"
author: "ECBS5294 — Working with Data"
titlepage: false
toc: false
geometry: margin=1in
---

# Homework 2 — Rubric

**ECBS5294 — Working with Data**

**Deliverable:** Homework 2 — Many tables and an API: the board-deck numbers
**Format:** `hw2-submission.zip` (made with `git archive` from your commits, as `SUBMITTING.md` says), uploaded to Moodle
**Total points:** 100

## Overview

You receive a colleague's half-done KPI notebook for a board deck. Four KPIs are drafted and their totals disagree with the headline numbers; two are not started; `BRIEF.md` defines all six. You capture the evidence for each failure before you repair it, repair each one with a query that follows the brief, build the two new KPIs, and prove every number with the identity that kind of number allows: a sum, a ratio's numerator and denominator, or a weighted mean. Every repaired or new KPI carries five parts in its markdown cell: the sentence, the rows you expected and why, the number, the check in the kind the heading names, and one line on how you would know if it were wrong. The three counts for every join live once, in section `J`, with an ID.

## How scoring works

Every criterion is scored at **exactly one of its three anchor values** — no in-between points. Read each table from the bottom up: a submission that meets **any** condition in the *Needs Improvement* row scores that; otherwise, one that meets **every** condition in the *Excellent* row scores Excellent; everything else scores *Satisfactory*. So each submission meets exactly one anchor. The rule picks the anchor; judging the evidence against each condition is still a grader's call, and borderline submissions are scored by two graders.

## Rubric

### 1. Correct fixes and the two new KPIs (30 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 30 | All of these: from a fresh unzip the notebook runs top to bottom after *Restart* and *Run All*, and all six KPI tables match the brief: monthly revenue in reais and euros at the month's average rate with every paid order in exactly one month; average order value as revenue over paid orders; average review score with one vote per review; item sales by category with every item kept and labelled; delivery time over delivered orders; revenue by seller state in euros. **The category repair is two queries**: the anti-join that lists every category with no English name and its item count, and the labelled `LEFT JOIN` that keeps every item. |
| **Satisfactory** | 20 | Neither of the other rows. For example: **one or two** KPIs do not match the brief (a daily or as-of conversion instead of the brief's monthly average; delivery time in fractional days); or a repair is **a one-token change** — `INNER` to `LEFT` with no anti-join and no labels, or a `LEFT JOIN` that leaves orders with no euro value — even if the total looks right. A one-token fix is Satisfactory at most. |
| **Needs Improvement** | 8 | Either of these: **three or more** KPIs do not match the brief or are missing; or the notebook does not run from a fresh unzip. |

**What we're looking for:** each repair written as a query that follows the brief's definition, not a keyword changed until a total matches. A number that matches the headline by a route the brief does not describe is not the fix.

### 2. The checks and the "how would I know" lines (30 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 30 | All of these: the reconciliation table has **a row per KPI naming the identity that KPI allows**, each beside a number computed independently by different code: the sums (revenue in reais and in euros, item sales, revenue by seller state) add up to their totals; the average order value reconciles its numerator and its denominator separately on the same orders; the review score and the delivery time recombine with their weights; the euro-over-reais ratio is checked against the range of monthly rates **and labelled as plausibility only**. The assertions cell encodes exactly those identities — money to the cent, `abs(a - b) < 0.005`, never `==` on sums of money — plus *no order without a rate* and *no order counted twice*; one assertion is shown failing on purpose, with its message pasted under `F`. Section `J` has **the three counts** — rows in the left table, rows in the result, distinct left keys in the result — **for every join in the submission**, each with an ID. Every KPI's check line names its row of `D` and its join IDs, and every KPI's **how-would-I-know line** names the number that would move, which way, and the cause as what the join did to the grain (or which rows found no match), citing the section A cell and the join ID that showed it — never the change. Every cited ID exists and shows what the line says. |
| **Satisfactory** | 20 | Neither of the other rows. For example: a KPI carries the wrong identity (an average that is summed, or recombined without its weights); an "independent" number reruns the KPI's own query; money is compared with `==`; one of the two named assertions is missing; no assertion was shown to fail; the three counts missing for some joins; one or two how-would-I-know lines that give the change as the cause ("I added DISTINCT") or cite nothing; a cited ID that does not exist, or does not show what the line says. |
| **Needs Improvement** | 8 | Any of these: no reconciliation table; no assertions cell, or none of its assertions can fail (a table compared with itself, `assert n > 0`); no three counts for any join; three or more how-would-I-know lines missing or giving the change as the cause; evidence that no query you ran produces. |

**What we're looking for:** the right identity for each kind of number, and a cause a colleague could check, from evidence you actually ran. Averages do not add up, and the average of averages is not the average. The range bound is a plausibility check; it cannot prove a conversion right. A join count for every join, written once and cited by ID, because the row count is where a join shows what it did.

### 3. The sentences and the sourced estimates, and the Results section (25 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 25 | All of these: every repaired and new KPI has a sentence in plain English that says what the number measures, which rows it comes from, and which rows the query left out, in the brief's terms; every one has the rows expected written **before** the query ran, with its source — the brief, a section A cell by ID, or "neither fixes this" — and a word on whether the file agreed; the README's **Results** section says what the six numbers show, what was excluded and why, and **one thing the data cannot answer**, stated as a fact about the data ("no cost data, so no margin"), not as a hedge, citing joins by ID where a sentence rests on one and copying no counts. |
| **Satisfactory** | 17 | Neither of the other rows. For example: one or two sentences that restate the KPI's name instead of naming the rows; an estimate with no source, or written after the fact; a Results section with no "cannot answer" line, or one that is a hedge ("more research is needed"), or that pastes join counts. |
| **Needs Improvement** | 6 | Any of these: three or more KPIs with no sentence or no estimate; no Results section; a sentence or estimate invented after the number ("I expected 26" with nothing behind it, on three or more KPIs). |

**What we're looking for:** that the number was understood before it was computed. A sentence that names the rows left out is the one that catches a join that kept too many or too few, and an estimate with a source is the one that catches a count that doubled.

### 4. Clean submission and Git (10 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 10 | All of these: `GIT_LOG.txt` shows a commit after each repair, with messages that name the cause, and **every commit that changes a join cites the join's ID** (its three counts live in section `J`, not in the message); `git status` was clean at the end; the archive contains what `SUBMITTING.md` lists and nothing it forbids. |
| **Satisfactory** | 6 | Neither of the other rows. For example: messages are "fix" or "update"; a join commit cites no join ID; one required file other than `GIT_LOG.txt` is missing from the archive. |
| **Needs Improvement** | 2 | Any of these: one commit only; no `GIT_LOG.txt`; two or more required files missing from the archive; `.venv/` in the archive; `data/raw/` edited. |

**What we're looking for:** a history a colleague can read, and an archive that runs from a fresh unzip.

### 5. AI use disclosure (5 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 5 | Specific, and consistent with the submission: what you asked, what it got right or wrong, and which query **you** ran to check it — for a join, the counts. Or an honest "did not use AI." |
| **Satisfactory** | 3 | Present and consistent with the submission, but generic. |
| **Needs Improvement** | 1 | Missing, or contradicted by the submission. |

**What we're looking for:** that you checked the assistant's joins and conversions with counts and the document's own fields, not with its explanation.

## General Notes

- The checks, the how-would-I-know lines, the sentences and the estimates together (55 points) outweigh the fixes (30). A notebook you cannot explain loses meaningful credit; a partial repair with clear evidence and an honest line on what would have broken it can still do well.
- A right number with no evidence and no identity could be right by accident. The rubric grades the query, the evidence and the check, not the number alone.
- AI tools are allowed. You must be able to explain everything you submit, in your own words, without notes.
- `git status` reporting a clean tree is the completeness check after a commit. An empty `git diff` alone is not: it does not see staged work or new files.
- Late: accepted up to one day late at −10%; nothing after Sunday 23:59 (syllabus, *Policies*).
