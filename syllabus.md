---
title: Syllabus
layout: default
nav_order: 2
---

# DS2 — Working with Data

**Program:** MSBA · **Credits:** 1.0 · **Academic year:** 2026–2027
**Format:** 3 teaching sessions × 200 minutes + a closed-book final exam in a separate session
**Instructor:** Eduardo Ariño de la Rubia · [RubiaE@ceu.edu](mailto:RubiaE@ceu.edu) · office hours by appointment

---

## What this course is for

Real data arrives messy. Types are wrong, values are missing in three different notations, there is no obvious key,
and the interesting part is buried in nested JSON that nobody documented. The analysis you actually want to run is
rarely the hard part — getting the data into a shape where that analysis is possible, and being able to show the
shape is right, is where the work goes.

This course is one credit of practical instruction in exactly that layer: SQL on DuckDB for one table and for many,
APIs and JSON into tidy tables, and a small pipeline whose checks can fail. Almost nothing in it crashes. The query
runs, the number comes out, and the number is wrong — a join tripled the revenue, a filter quietly dropped every row
with a missing value. The course's thesis is one sentence: **no error is not the same as correct.** The skill is to
look for evidence of wrongness before anyone complains, and then to build the check into the code so that next time
it fails out loud.

---

## What you will be able to do

1. **State the grain and the key of a table**, and prove the key with a query.
2. **Read a column's type** and say what it did to the values; decide what "missing" means, and state what `NULL` does to a filter, a count, an average, and a join.
3. **Answer a business question from one table in SQL** — filter, calculate, aggregate, group — and say how many rows to expect.
4. **Join tables without corrupting the result** — choose the join the question needs, count before and after, find the unmatched rows, and aggregate at the right grain.
5. **Turn a JSON document or an API response into tidy tables**, one table per grain, and persist them.
6. **Build a bronze/silver/gold pipeline** that runs from a fresh clone, leaves the raw data untouched, and validates itself with checks that can fail.
7. **Reconcile every number** against an independent computation before reporting it.
8. **Say what a number shows** — what it assumes, which rows it excludes and why, and what the data cannot answer.

---

## Format and workload

Each session runs two blocks of a 45-minute lecture plus a 45-minute hands-on lab, with a break between. Sessions 2
and 3 open with a 10-minute closed-book knowledge check. In the lecture you predict, then watch: every query is on a slide, you write down what you
expect, and the next slide shows what it printed; laptops are for notes. You type in the lab — each lab's project downloads
during the five-minute stretch before it. The labs are where the learning happens:
each hands you a colleague's report that runs without an error and prints a wrong number; you find out why, write the
query the question actually needs, add the check that would have caught it, and explain it to the person next to you.

Expect about 20–25 hours of work outside class, most of it on the three homeworks: about 3–4 hours for Homework 1
(the shortest, because it shares its week with DS1's last homework; its two stretch questions are not graded), 6–7 for Homework 2, 8–10 for Homework 3, and 3–4
preparing for the exam, the mock and the Knowledge check 3 retry included. These are estimates; if you are well past one, say so on the Moodle forum. It is not a
mark against you.

---

## Prerequisites and setup

**DS1 — Computing for Analytical Work (ECBS5293)**, and everything it left you with: the terminal, `uv`, VS Code and
the `.venv` kernel, paths from the project folder, Git from `git status` to `git archive`, the five-part diagnosis
note, and explaining your fix to a neighbour. This course re-teaches none of it. Windows users work in **Git Bash**,
as in DS1. DuckDB arrives with each project's `uv sync`: there is nothing new to install. No SQL is assumed. Bring a
laptop to every session.

**Before Session 1**, run the setup check (the [Pre-course setup](site/setup.html) page) and submit its output to
the Moodle "Setup verification" slot. It walks the whole path once — clone, sync, run, notebook, commit,
`git archive`, submit — so the first thing you submit in this course is the setup, not Homework 1.

**If you did not take DS1**, the [Bridge from DS1](site/bridge.html) page lists the six DS1 habits this course
assumes, each with the DS1 page that teaches it and a short exercise. Do it before Session 1. The exercises are
practice; the setup check is the evidence. When you submit the setup check, say in the Moodle text box whether you
took DS1 and roughly how long setup took you. If you did not take DS1, or setup failed or took you more than an hour,
book a supported 30-minute rehearsal with a TA in the week of 28 September, on Moodle. The [Glossary](site/glossary.html) defines every term used here, and the
[SQL and pipeline reference](site/reference.html) has every query pattern the course teaches.

---

## AI policy

You may use AI assistants to explain errors, suggest diagnostic steps, teach you concepts, and improve your writing.
You may not use AI to submit code or explanations you cannot defend in person, run commands you cannot explain, or
describe work you did not do. Two labs — Labs 3 and 5 — open with 20 minutes of no-AI time: practice for the
closed-book exam, announced in session.

**AI writes SQL that runs. Running is not right.** An assistant given your schema will write a join that returns
rows, and it will not tell you that the rows are tripled. The counts are yours to run. When you ask an assistant
about a query, give it the schema and the grain of each table, the number you expected and the number you got, and
ask for the checks that would tell them apart — then run those checks yourself.

You remain fully responsible for everything you run, change, and submit. The assessment design assumes you used AI;
it checks whether you understood the result.

---

## Assessment

| Component | Weight |
|---|---:|
| Homework 1 — One table, the weekly numbers | 10% |
| Homework 2 — Many tables and an API: the board-deck numbers | 15% |
| Homework 3 — Handover: the emissions brief | 15% |
| Knowledge check 2 (start of Session 2, closed-book) | 5% |
| Knowledge check 3 (start of Session 3, closed-book) | 5% |
| Final exam (closed-book, in person, separate session) | 50% |

**Homework** is a larger lab, graded against a rubric published with each assignment. Every submission includes a
written diagnosis note — symptom, cause, evidence, change, verification, with one entry for each data problem that
changed a number — and a short video walkthrough in your own voice (60–90 seconds; up to two minutes for Homework 3).
The explanation carries more weight than the fix: a right number you cannot explain loses meaningful credit; a
partial answer with an honest diagnosis can still do well. A self-check key for each homework is posted on Moodle
after its late window closes. Do not expect graded feedback on Homework 3 before the exam: its self-check key and the
mock exam with its key are that feedback.

**Labs carry no points.** A lab is complete when you have explained your work to a classmate in the last ten minutes
of the lab and uploaded your diagnosis note to that lab's Moodle checkpoint. The checkpoint is a completion record,
not graded work: staff read every note that asks for help, and a sample of the rest. Labs cost you preparation if
skipped, not points.

**Knowledge checks** are 10-minute closed-book checks on the previous session's material. Each is 5%. One question in
each asks you to write a short query by hand. Your marked paper comes back to you after grading, with the check's key:
the key's model queries, scored clause by clause, are how the exam's written queries are marked. After Knowledge
check 3, everyone gets a short **retry**: one query of the same kind, posted on Moodle on Monday 19 October, answered
by Wednesday 21 October, and returned by Friday 23 October with its model answer and one correction. The retry is
**ungraded** and is not a make-up: it is a second attempt at the query the exam tests again.

**The final exam** is closed-book, individual, in person, about 70 minutes, in a separate session after Homework 3 is
due; the program calendar sets its date, and Moodle announces it. It tests the same skills as the homeworks on new
scenarios: ten multiple-choice questions on short scenarios (40 points), four queries you write by hand and that are
scored clause by clause (40 points), and one diagnosis note that ends with the two sentences you would send a manager
— what the corrected number shows, and what it cannot (20 points). A mock
exam in the same format is posted on Moodle on Monday 19 October, after Session 3, so you can sit it, check your own
answers against the course materials, and try again; its self-check key follows with Homework 3's, on Sunday 1
November.

---

## Schedule

| Session | Date | Arc | Homework |
|---|---|---|---|
| Setup check | before Session 1 | Clone, sync, run, commit, archive, submit — once | submit to Moodle before Session 1 |
| 1 — What is this data? | Mon 5 Oct 2026 | Grain, keys, types, and missing values; one table in SQL: rows, then groups | HW1 due **Fri 9 Oct, 23:59** |
| 2 — What happens when tables meet? | Mon 12 Oct 2026 | Joins and grain, and finding the keys that repeat; APIs and JSON into tables, and shares of a total | HW2 due **Fri 16 Oct, 23:59** |
| 3 — Can I trust the answer? | Mon 19 Oct 2026 | Bronze, silver, gold, and casting text safely; validations as code; the note that goes with the number | HW3 due **Fri 30 Oct, 23:59** |
| Final exam | separate session | Closed-book, about 70 minutes | date set by the program calendar, on Moodle |

Self-check keys are posted on **Sunday 11 October** (HW1), **Sunday 18 October** (HW2), and **Sunday 1 November**
(HW3, with the mock exam's key). The mock exam's questions and the ungraded Knowledge check 3 retry are posted on
**Monday 19 October**, after Session 3. All deadlines are on Moodle, which is authoritative.

---

## Submitting your work

Everything is submitted through **Moodle**; nothing is graded from GitHub. You clone each lab and homework from a
public starter repository and work locally; each homework's `SUBMITTING.md` gives the exact commands — commit, write
`GIT_LOG.txt`, make the zip with `git archive` — and you upload the zip and your video. Submissions must run as
instructed from a fresh unzip and contain nothing you cannot explain. The zip carries the course's copy of
third-party data under each dataset's own license; it is for grading only.

---

## Policies

- **Late homework:** accepted up to one day late at −10% (nothing after Saturday 23:59, so each self-check key can post on the Sunday — before Monday's knowledge check for HW1 and HW2, and with the mock exam's key for HW3).
- **Extensions:** ask before the deadline, by email, with a reason. Documented illness or emergencies are always accommodated.
- **Missed knowledge check:** a documented absence moves that 5% to the final exam; an undocumented absence scores 0. No make-up checks.
- **Missed lab:** do it at home that week and submit the diagnosis note to its Moodle checkpoint; say in the note if you want someone to look at it.
- **Regrading:** within 7 days, in writing, naming the rubric criterion you believe was misapplied. The whole submission is re-read; the grade can move either way.
- **Oversubscription:** this is a core MSBA course. If it is oversubscribed, seats go to MSBA students first, then EDP and data-track students, then others as space permits — in order of sign-up and subject to program rules.
- **Grade conversion:** CEU letter scale — A 94+ · A- 88–93 · B+ 80–87 · B 71–79 · B- 63–70 · C+ 58–62 (minimum pass) · F below.

---

## Academic integrity and accessibility

CEU academic integrity and accessibility policies apply; contact me and the relevant university office early if you
need accommodations. If speaking aloud is a barrier, the end-of-lab explanation can be written for your partner to
read, and homework videos can be completed in writing or by audio with the same content requirements — arrange it
with me in advance. Accessible-format exam papers are available on request per your CEU accommodation letter; ask at
least a week ahead.
