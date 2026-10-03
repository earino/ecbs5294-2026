---
title: "Homework 3 Rubric"
subtitle: "Handover: the emissions brief"
author: "ECBS5294 — Working with Data"
titlepage: false
toc: false
geometry: margin=1in
---

# Homework 3 — Rubric

**ECBS5294 — Working with Data**

**Deliverable:** Homework 3 — Handover: the emissions brief
**Format:** `hw3-submission.zip` (made with `git archive` from your commits, as `SUBMITTING.md` says), uploaded to Moodle. Due Saturday 31 October 2026, 23:59; late window: one day, at −10%. Moodle is authoritative.
**Total points:** 100

## Overview

A ministry wants one gold table and one note: greenhouse-gas emissions per person and per unit of GDP for the EU-27, 2010–2024, by source sector, built from three sources in three formats — Eurostat (TSV), Our World in Data (CSV), and the World Bank (cached API pages, JSON). The README's analytical contract fixes every decision: the gas, the unit, the sectors, the scope, the membership, the final year, the denominators. You write the three cleaners, the four check families, and the gold report into a supplied pipeline; you document silver in a data dictionary, with a paragraph for every trap the data set; and you write the note, with one line per number saying how you would know if it were wrong. Because this is the last homework, it ends with the end-of-course reflection.

The grader unzips your submission, runs `uv sync` and `uv run python pipeline.py`, then runs the **mutation harness** described in the README: once on the course's unchanged raw files, then four times, each on a copy with one raw file corrupted in a way designed for one check family. It reads `output/checks_report.json`. Do not expect graded feedback before the exam: when the late window closes, a self-check key is posted on Moodle. The mock exam is posted after Session 3; its key comes with this self-check key.

## How scoring works

Every criterion is scored at **exactly one of its three anchor values** — no in-between points. Read each table from the bottom up: a submission that meets **any** condition in the *Needs Improvement* row scores that; otherwise, one that meets **every** condition in the *Excellent* row scores Excellent; everything else scores *Satisfactory*. So each submission meets exactly one anchor. The rule picks the anchor; judging the evidence against each condition is still a grader's call, and borderline submissions are scored by two graders.

## Rubric

### 1. Correct fix: the pipeline and its gold table (18 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 18 | From a fresh unzip, `uv sync` and `uv run python pipeline.py` complete and write gold. `gold.intensity` matches the key's numbers: the EU-27 per person and per unit of GDP, by sector, for 2010 and 2024, and every member's 2024 total. The contract is followed exactly (the unit, the seven codes, the 27 members, 2010–2024, the World Bank denominators, the EU-27 as a ratio of sums). Every cleaner returns the full accounting — rejected, excluded, quarantined, aggregate, retained — and it adds up to what came in. The World Bank cleaner carries each page's number down to every record, and its page counts set what each page promises beside what was read from it. `data/raw/` is untouched. |
| **Satisfactory** | 12 | The pipeline runs from a fresh unzip, `data/raw/` is untouched, and gold departs from the key in at most one way (one contract decision not followed, such as the EU-27 as an average of 27 ratios or a different total, or one source read incompletely) — but at least one Excellent requirement is not met: gold departs in that one way; or a cleaner's accounting is missing or does not add up; or the page is not carried down or the page counts are missing. Documentation is not scored here: an incomplete dictionary costs points in criterion 7 only. |
| **Needs Improvement** | 4 | The pipeline does not run from a fresh unzip; or gold departs from the key in two or more ways; or `data/raw/` was edited. |

**What we're looking for:** a table the ministry can rebuild from your commits alone, whose every number follows the contract. The documentation that shows you found each trap, rather than being lucky, is criterion 7.

### 2. Verification (15 points)

The harness grades each check family on its own corruption. A family **catches** it when its own record in `output/checks_report.json` has an action the brief permits for that corruption (key: `stop` or `dedupe`; domain: `quarantine`; count: `stop` or `exclude`; reconciliation: `stop`), its `subject` names the corrupted silver table, and its `observed` value moved from the clean run. A green, silent run is a miss, however the code looks; so is a stop reported by the wrong family. The clean run must pass: every family at least one record, all `pass`, and the three silver tables named. **All four families are required in this homework**, the domain family included: a family with no records is a missed family, and so is a family whose own checks do not all `pass` on the unchanged files. A reconciliation compares the gap `abs(a - b)` with the contract's tolerance; one written with `==` fails on the clean files, and so fails the clean run.

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 15 | The clean run completes, all four families catch their corruption, and Appendix A of `NOTE.md` states each reconciliation — the identity, its two numbers, and where each came from. |
| **Satisfactory** | 10 | The clean run completes, and either exactly three families catch their corruption, or all four do and Appendix A falls short of Excellent (missing, or an identity without its two numbers or their source). |
| **Needs Improvement** | 4 | The clean run does not complete — a crash, a stop, an unfinished stage, a silver table no check names, or `output/checks_report.json` missing or malformed — in which case no corruption is run; or it completes and two or fewer families catch their corruption. |

**What we're looking for:** checks that fail when the data is wrong, and only then — and a report that says which check, about which table, and what it saw. Run your own corruptions before you submit: the README shows how.

### 3. The note, and how you would know if it were wrong (22 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 22 | `NOTE.md` is five to nine sentences a reader without SQL can act on: the metric (what is divided by what, for which countries and years, in which unit); the EU-27 figures for 2010 and 2024 and the 2024 leaders on both measures, matching gold; what was left out and how many; the assumptions a reader could miss; the reconciliation in plain words; OWID against Eurostat in a sentence or two (the median residual, how much the two sources' documentation explains, and that the rest is unexplained); and **what the data cannot answer, stated as a claim about the data** ("this cannot tell you X, because the data has no Y"). **Appendix B** has one line for every number the note quotes, saying how that number could be wrong and what you would see if it were — a check by its name, a row of Appendix A, an accounting line, or a dictionary paragraph — and every line points at evidence that exists. |
| **Satisfactory** | 15 | The note defines its metric and is about the result, and one or two of the other elements are missing or wrong. A "cannot answer" that is a hedge ("more research is needed", "data quality may vary") rather than a claim about the data counts as one element wrong; so does an OWID–Eurostat sentence that is missing or explains nothing; so does Appendix B with a quoted number that has no line, or a line that names no evidence ("I checked it carefully"). |
| **Needs Improvement** | 5 | The metric is not defined (numbers without what is divided by what); or three or more of the other elements are missing or wrong; or the note describes the code instead of the result; or Appendix B is missing. |

**What we're looking for:** the sentence that goes with the number, and the line that says how you would know if the number were wrong. A decision-maker should know what the number is, what it leaves out, what it cannot tell them, and that someone checked.

### 4. End-of-course reflection (23 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 23 | All four specifics present in `REFLECTION.md`: a quoted sentence from one of your three notes, and why it is your strongest; a step of the loop you changed or deliberately kept, with a concrete example and what it cost or saved; the failure class you were slowest at (grain, NULLs, joins, JSON, cleaning, or checks) and a concrete plan; how your use of AI changed, with one example — or, if you did not use it, how you verified your work instead, with one example. Every specific can be met honestly by every student: none requires a confession or a particular story. |
| **Satisfactory** | 15 | Exactly three of the four specifics; the rest is general. |
| **Needs Improvement** | 5 | Two or fewer of the four specifics: generalities, or a reflection that could have been written without re-reading your own work. |

**What we're looking for:** honesty and specificity. 200 concrete words beat 300 padded ones; 300 is a ceiling.

### 5. Clean submission and Git hygiene (10 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 10 | `GIT_LOG.txt` shows a sequence of commits whose messages say what each stage does and what it found, ending in a clean state; raw data committed and nothing generated (`data/silver/`, `data/gold/`, `output/`, `.venv/`) in the archive; every file `SUBMITTING.md` lists is present. |
| **Satisfactory** | 6 | None of the Needs Improvement conditions, but one or more of: a stray file that is neither generated nor required (a scratch notebook, a copy of a script); a missing required file that the pipeline does not need; commit messages that say nothing. |
| **Needs Improvement** | 2 | No commits beyond the starter's; or generated files (`data/silver/`, `data/gold/`, `output/`, `.venv/`) in the archive; or the pipeline cannot run from what was committed. |

**What we're looking for:** raw is committed, generated is ignored, and before archiving you knew exactly what changed.

### 6. AI use disclosure (5 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 5 | Specific and consistent with the submission: what you asked, what it got right or wrong, and which query you ran to check it. Or an honest "did not use AI." |
| **Satisfactory** | 3 | Present, not contradicted by the submission, but generic. |
| **Needs Improvement** | 1 | Missing, or contradicted by the submission. |

**What we're looking for:** AI writes SQL that runs. Running is not right — say how you knew.

### 7. Documentation: the data dictionary (7 points)

| Level | Points | Criteria |
|---|---|---|
| **Excellent** | 7 | `docs/dictionary.md` documents every silver table — its key, and every column's type, meaning, unit, allowed values, what missing means, and what was done to it (`page` included). Under each source, *What this file does that changed a number* has a paragraph, with pasted evidence, for every trap that would have changed a gold number, each naming the check that would fail if it came back; the World Bank section opens with the prediction written before the query. |
| **Satisfactory** | 5 | Neither of the other rows. For example: a silver table or some columns' cells missing from the dictionary; a trap that would have changed a gold number with no paragraph, or a paragraph without evidence or without its check; the World Bank section without its prediction. |
| **Needs Improvement** | 2 | No dictionary, or the scaffold's dictionary unfilled; or no *What this file does* paragraphs at all, or paragraphs that paste no evidence. |

**What we're looking for:** a reader who never saw your code can say what each silver column means, and see, trap by trap, that you found each problem before it could change a number. A right number with no paragraph for the trap it survived could have been right by accident; that costs points here, and only here.

## General Notes

- The note and the reflection together (45 points) outweigh the gold table (18) and its documentation (7). This is deliberate: the course grades whether you can show and say why a number is right, not only the number.
- The full accounting (practised in Lab 5) and all four check families (practised in Lab 6) are required in this homework. If you missed either lab or did not finish it, the Block 5 and Block 6 slides and the course site's *SQL and pipeline reference* (sections 8–10) show both.
- Each output is pasted once, in the dictionary; the note's Appendix B and the column cells refer to it, and every paragraph names its check. Pasting the same output twice earns nothing more.
- The contract's decisions are the ministry's: a different unit, total, membership, or final year is a different table, graded as one.
- The verification criterion is graded by a program, the same way for everyone. The report format in the README is required; the harness reads only its six fields.
- The OWID–Eurostat comparison is reported, never checked: no tolerance applies to it and its size is not graded. Explaining it beyond the two sources' documentation earns nothing extra.
- Never edit `data/raw/`. The harness grades every submission on the course's own raw files.
- AI tools are allowed. You must be able to explain everything you submit, in your own words, without notes.
- Re-read your three notes before writing the reflection — that re-reading is the point, and the best exam preparation you have.
