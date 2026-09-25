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
**Format:** `hw2-submission.zip` (made with `git archive` from your commits, as `SUBMITTING.md` says) and the video, both uploaded to Moodle
**Total points:** 100

## Overview

You receive a colleague's half-done KPI notebook for a board deck. Four KPIs are drafted and their totals disagree with the headline numbers; two are not started; `BRIEF.md` defines all six. You capture the evidence for each failure before you repair it, repair each one with a query that follows the brief, build the two new KPIs, and prove every number with the identity that kind of number allows: a sum, a ratio's numerator and denominator, or a weighted mean.

## How scoring works

Every criterion is scored at **exactly one of its three anchor values** — no in-between points. Read each table from the bottom up: a submission that meets **any** condition in the *Needs Improvement* row scores that; otherwise, one that meets **every** condition in the *Excellent* row scores Excellent; everything else scores *Satisfactory*. So each submission meets exactly one anchor, and two graders reach the same number. The video is scored per element.

## Rubric

### 1. Correct fixes and the two new KPIs (30 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 30 | All of these: from a fresh unzip the notebook runs top to bottom after *Restart* and *Run All*, and all six KPI tables match the brief: monthly revenue in reais and euros at the month's average rate with every paid order in exactly one month; average order value as revenue over paid orders; average review score with one vote per review; item sales by category with every item kept and labelled; delivery time over delivered orders; revenue by seller state in euros. **The category repair is two queries**: the anti-join that lists every category with no English name and its item count, and the labelled `LEFT JOIN` that keeps every item. |
| **Satisfactory** | 20 | Neither of the other rows. For example: **one or two** KPIs do not match the brief (a daily or as-of conversion instead of the brief's monthly average; delivery time in fractional days); or a repair is **a one-token change** — `INNER` to `LEFT` with no anti-join and no labels, or a `LEFT JOIN` that leaves orders with no euro value — even if the total looks right. A one-token fix is Satisfactory at most. |
| **Needs Improvement** | 8 | Either of these: **three or more** KPIs do not match the brief or are missing; or the notebook does not run from a fresh unzip. |

**What we're looking for:** each repair written as a query that follows the brief's definition, not a keyword changed until a total matches. A number that matches the headline by a route the brief does not describe is not the fix.

### 2. Verification: the reconciliation table and the assertions (20 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 20 | All of these: the reconciliation table has **a row per KPI naming the identity that KPI allows**, each beside a number computed independently by different code: the sums (revenue in reais and in euros, item sales, revenue by seller state) add up to their totals; the average order value reconciles its numerator and its denominator separately on the same orders; the review score and the delivery time recombine with their weights; the euro-over-reais ratio is checked against the range of monthly rates **and labelled as plausibility only**. The assertions cell encodes exactly those identities — money to the cent, `abs(a - b) < 0.005`, never `==` on sums of money — plus *no order without a rate* and *no order counted twice*, and one assertion is shown failing on purpose, with its message pasted. |
| **Satisfactory** | 13 | Neither of the other rows. For example: a KPI carries the wrong identity (an average that is summed, or recombined without its weights); an "independent" number reruns the KPI's own query; money is compared with `==`; one of the two named assertions is missing; no assertion was shown to fail. |
| **Needs Improvement** | 5 | Any of these: no reconciliation table; no assertions cell; or no assertion in it can fail (a table compared with itself, `assert n > 0`). |

**What we're looking for:** the right identity for each kind of number. Averages do not add up, and the average of averages is not the average. The range bound is a plausibility check; it cannot prove a conversion right.

### 3. Diagnosis notes and the three counts for every join (20 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 20 | All of these: one five-part note per failure (four), each cause stated as what the query did to the grain (or which rows found no match), each supported by evidence from *before* the repair — section A's cells and the joins, cited by ID with the line that shows the cause — and each verification naming its row of `D`, its assertion in `E`, and the pasted message from making it fail; a **Joins** section with **the three counts** — rows in the left table, rows in the result, distinct left keys in the result — **for every join in the submission**, each with an ID; every cited ID exists and shows what the note says; a trap log with the data issues that changed a number; the README's **Results** section says what the numbers show, what was excluded, and one thing the data **cannot answer**, stated as a fact about the data ("no cost data, so no margin"), not as a hedge. |
| **Satisfactory** | 13 | Neither of the other rows. For example: one or two notes whose cause restates the change ("I added DISTINCT") or that have no evidence; the three counts missing for some joins; a cited ID that does not exist, or does not show what the note says; no trap log; no "cannot answer" line, or one that is a hedge ("more research is needed"). |
| **Needs Improvement** | 5 | Any of these: three or more of the four notes give the change as the cause, or have no evidence; no three counts for any join; evidence that no query you ran produces. |

**What we're looking for:** a cause a colleague could check, from evidence you actually ran, and a join count for every join, written once in the Joins section and cited by ID, because the row count is where a join shows what it did. Pasting the same counts into several places earns nothing.

### 4. Video walkthrough (15 points)

Scored per element: **full**, **half** (vague or wrong), or **0** (absent). The video takes **one** failure of your choice, explained in full; nothing is asked or scored about the other three, which are in your notes.

| Element | Full | Half | What earns full |
|---|---|---|---|
| Symptom | 3 | 1 | The chosen failure's two numbers that should agree and did not |
| Cause | 5 | 2 | Why the number was wrong — the grain of the join's result, or the rows with no match — not only where |
| Change | 3 | 1 | The query as it is now, and why it follows the brief |
| Verification | 4 | 2 | The notebook after *Restart* and *Run All* with the assertions passing, and the identity that now holds, with both of its numbers |

Your voice is required: a silent recording scores 0 on all four elements. Open by naming the homework and the repo; a missed name is a clean-submission matter (criterion 5), not a video one. 60–90 seconds. Excellent / Satisfactory / Needs Improvement map to 13–15 / 8–12 / 0–7.

*Approved alternative formats* (see the syllabus's accessibility section — arrange in advance): a written walkthrough with annotated screenshots, or an audio-only recording, is scored on the same four elements at the same values. The "your voice" requirement applies to the standard video route only, never to an arranged accommodation.

### 5. Clean submission and Git (10 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 10 | All of these: `GIT_LOG.txt` shows a commit after each repair, with messages that name the cause, and **every commit that changes a join carries its three counts** (one line; the join's ID helps the reader); `git status` was clean at the end; the archive contains what `SUBMITTING.md` lists and nothing it forbids; the video opens by naming the homework and the repo. |
| **Satisfactory** | 6 | Neither of the other rows. For example: messages are "fix" or "update"; a join commit lacks its counts; one required file other than `GIT_LOG.txt` is missing from the archive; the video does not open with the names. |
| **Needs Improvement** | 2 | Any of these: one commit only; no `GIT_LOG.txt`; two or more required files missing from the archive; `.venv/` in the archive; `data/raw/` edited. |

**What we're looking for:** a history a colleague can read, and an archive that runs from a fresh unzip.

### 6. AI use disclosure (5 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 5 | Specific, and consistent with the submission: what you asked, what it got right or wrong, and which query **you** ran to check it — for a join, the counts. Or an honest "did not use AI." |
| **Satisfactory** | 3 | Present and consistent with the submission, but generic. |
| **Needs Improvement** | 1 | Missing, or contradicted by the submission. |

**What we're looking for:** that you checked the assistant's joins and conversions with counts and the document's own fields, not with its explanation.

## General Notes

- The notes and the video together (35 points) outweigh the fixes (30). A notebook you cannot explain loses meaningful credit; a partial repair with clear evidence and an honest note can still do well.
- A right number with no evidence and no identity could be right by accident. The rubric grades the query, the evidence and the check, not the number alone.
- AI tools are allowed. You must be able to explain everything you submit, in your own words, without notes.
- `git status` reporting a clean tree is the completeness check after a commit. An empty `git diff` alone is not: it does not see staged work or new files.
- Late: accepted up to one day late at −10%; nothing after Saturday 23:59 (syllabus, *Policies*).
