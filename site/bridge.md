---
title: Bridge from DS1
layout: default
nav_order: 5
---

# Bridge from DS1 — for students who did not take it

This course follows **DS1 — Computing for Analytical Work** (ECBS5293) and re-teaches none of it. If you did not
take DS1, this page is your way in: the six DS1 habits this course assumes from its first minute, each with the DS1
page that teaches it and an exercise of about five minutes. Do it **before Session 1**.

**The exercises are practice; the [setup check](setup.html) is the evidence.** Nobody checks the exercises, and
finishing them in five minutes each does not show that the tools work for you. The setup check walks the same path
end to end, on your laptop, and you submit it.

DS1's materials are public: [earino.github.io/ecbs5293](https://earino.github.io/ecbs5293/). Its slides have the
full explanation; you do not need to read all of them. Install the tools first, from DS1's
[Pre-course setup](https://earino.github.io/ecbs5293/site/setup.html), §1.

Every exercise below uses the setup project, so start by cloning it, in your terminal, in the folder where you keep
course work:

```bash
git clone https://github.com/earino/ecbs5294-setup.git
```

## 1. Work in the terminal

**The habit:** you know where you are (`pwd`), what is here (`ls`), and how to move (`cd`), and you use one shell for
everything — **Git Bash** on Windows, Terminal on macOS.

**Taught in DS1:** [Block 2 — The terminal as a way to inspect reality](https://earino.github.io/ecbs5293/units/session1/block2/slides.html).

**Five minutes:**

```bash
pwd                  # where am I?
ls                   # what is here? You should see ecbs5294-setup
cd ecbs5294-setup
ls                   # README.md, check.py, data, pyproject.toml, ...
ls data/raw          # online_retail.parquet
cd ..
pwd                  # back where you started
```

You can say, for each line, what it printed and why.

## 2. Run a project in its own environment with `uv`

**The habit:** every project has its own Python and packages, in its own `.venv/` folder. `uv sync` builds it from
the project's `pyproject.toml` and `uv.lock`; `uv run` runs a command inside it. Nothing is installed globally.

**Taught in DS1:** [Block 3 — Python interpreters, packages, imports, and environments](https://earino.github.io/ecbs5293/units/session2/block1/slides.html).

**Five minutes:**

```bash
cd ecbs5294-setup
uv sync
uv run python -c "import sys; print(sys.executable)"      # a path inside this folder's .venv
uv run python -c "import duckdb; print(duckdb.__version__)"   # 1.5.5
```

You can say why the first path contains `.venv`, and what would happen if you ran a notebook on a different Python.

## 3. Run a notebook on the project's kernel, top to bottom

**The habit:** open the **project folder** in VS Code (not the file), pick the kernel whose path contains `.venv`,
prove it with `sys.executable`, and treat *Restart and Run All* as the only test that a notebook works. Outputs left
on the screen from an earlier session prove nothing.

**Taught in DS1:** [Block 4 — Notebooks, scripts, state, and reproducibility](https://earino.github.io/ecbs5293/units/session2/block2/slides.html),
and the *Notebook standard* on DS1's [setup page](https://earino.github.io/ecbs5293/site/setup.html).

**Five minutes:** in VS Code, *File → Open Folder…* → `ecbs5294-setup`. Open `check_notebook.ipynb`, *Select Kernel*
→ *Python Environments…* → the `.venv` entry. *Run All*. Then *Restart*, and *Run All* again: the same three
outputs. Do not save it yet; the setup check does that.

## 4. Paths are read from where you run

**The habit:** a relative path such as `data/raw/online_retail.parquet` is looked up from the working directory, not
from where the script lives. Every command in this course is run from the project folder, and every notebook starts
by anchoring itself there (the `PROJECT_ROOT` cell).

**Taught in DS1:** [Block 1 — Files, folders, paths, and working directories](https://earino.github.io/ecbs5293/units/session1/block1/slides.html).

**Five minutes:** from the project folder, then from a folder inside it:

```bash
uv run python check.py          # the "query the data file" line is ok
cd data
uv run python ../check.py       # the same script: FIX THESE FIRST, the data file is not found
cd ..
```

You can say why the same script, reading the same file, fails from one folder and not the other.

## 5. Git as a safety net, and as the way you submit

**The habit:** `git status` before and after everything; `git diff` to see what you changed; `git add` and
`git commit` with a message that says *why*; `git restore` to throw away a change; and `git archive` to make the zip
you submit, which contains exactly what you committed.

**Taught in DS1:** the Git thread of every block, from [Block 2](https://earino.github.io/ecbs5293/units/session1/block2/slides.html)
on; `git archive` in DS1's [Homework 2 README](https://github.com/earino/ecbs5293-hw02-python-environments-notebooks).

**Five minutes:**

```bash
cd ecbs5294-setup
git status                 # clean
echo "practice" >> README.md
git status                 # README.md modified
git diff                   # the line you added
git restore README.md
git status                 # clean again
```

Then read steps 4 and 5 of the setup check's README: you will commit and archive for real there.

## 6. The diagnosis note, and explaining it to a neighbour

**The habit:** when something is wrong, you write five short parts — **symptom** (what you saw, compared with what),
**cause** (why, not what you changed), **evidence** (pasted output, not a memory), **change**, **verification** (the
check that passes now and would fail if the problem came back). Every lab ends with explaining it, out loud, to the
person next to you, who asks: *show me the evidence; why did it fail, not just where; and what if…?*

**Taught in DS1:** every lab; the model is DS1's
[Lab 1 README](https://github.com/earino/ecbs5293-lab01-paths), sections *Diagnosis note* and *The last ten minutes*,
and the [DS1 syllabus](https://earino.github.io/ecbs5293/syllabus.html), *Assessment*.

**Five minutes:** exercise 4 failed on purpose. Write its five parts in five sentences. The cause is a sentence
about the working directory; the evidence is what `check.py` printed; the change is where you ran it from; the
verification is the line that now says `ok`. This course's notes add one thing — the evidence is a query and its
output — and the labs show you how.

## Done?

Run the [setup check](setup.html) and submit it. In the Moodle text box, say **whether you took DS1** and **roughly
how long setup took you**, and name any exercise above that did not behave as described: that is exactly what we need
to know before Session 1.

If you did not take DS1, or setup failed or took you more than an hour, **book a supported 30-minute rehearsal with a TA** in the week of 28 September, on Moodle. You run the path once more with someone beside you, so that Session 1's lab is
about the data, not the tools. Book it as early as you can: the slots are in the week before Session 1.
