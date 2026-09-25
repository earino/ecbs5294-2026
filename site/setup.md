---
title: Pre-course setup
layout: default
nav_order: 4
---

# Pre-course setup — due before Session 1

**If you took DS1, your laptop is already set up.** This course uses the same Python 3.13, `uv`, VS Code, Git, and
(on Windows) Git Bash. The one new tool is DuckDB, and you do not install it: every project in this course pins
`duckdb==1.5.5` in its `pyproject.toml`, and `uv sync` puts it in that project's `.venv/`.

**Everyone runs the setup check** and submits it to the Moodle **Setup verification** slot before Session 1. Session 1
cannot be tech support.

## 1. Did not take DS1?

Two things, before the check:

1. **Install the tools.** DS1's [Pre-course setup](https://earino.github.io/ecbs5293/site/setup.html), §1, has the
   per-OS steps for Git (Git for Windows, which includes **Git Bash**), `uv`, and VS Code with its Python and Jupyter
   extensions. Python 3.13 arrives by itself with the first `uv sync`.
2. **Do the [Bridge from DS1](bridge.html).** It lists the six DS1 habits this course assumes, each with the DS1
   page that teaches it and a short exercise. The exercises are practice; the setup check below is the evidence.
3. **Book a supported rehearsal.** After you submit the setup check, book a 30-minute rehearsal with a TA in the week
   of 28 September, on Moodle: you run the path once more with someone beside you, before Session 1 depends on it.

## 2. Run the setup check

The setup check is a small project, [`earino/ecbs5294-setup`](https://github.com/earino/ecbs5294-setup), with nothing
broken in it. Its README walks every step; this is the short version. In your terminal (Git Bash on Windows,
Terminal on macOS), in the folder where you keep course work:

```bash
git clone https://github.com/earino/ecbs5294-setup.git
cd ecbs5294-setup
uv sync
uv run python check.py
```

It prints one line per check and ends in **`ALL CHECKS PASSED`**, or in **`FIX THESE FIRST`** with what to do about
each problem. Fix, run it again. When it passes, keep its output: `uv run python check.py > check_output.txt`.

Then the half a script cannot check: open the **folder** in VS Code, open `check_notebook.ipynb`, pick the **`.venv`**
kernel, **Run All**, and save. Three outputs: a path containing `.venv`, `duckdb 1.5.5`, and a table with 525461 in
it — the number of invoice lines in the file the check reads.

## 3. What it checks

| Check | Why this course needs it |
|---|---|
| Python is this project's `.venv` Python, 3.13 | every notebook and script runs on the project's own environment |
| `import duckdb` gives **1.5.5** exactly | how DuckDB reads a file can change between versions; every number in the course's labs was computed with this one |
| `pandas` and `ipykernel` import | notebooks run in VS Code and show query results as tables |
| one query on `data/raw/online_retail.parquet` returns 525,461 rows | DuckDB reads a real file, from a path written from the project folder, as every lab does |
| `git` and `uv` are on your PATH; Git knows your name and email | every homework is committed and archived with Git |
| on Windows: the shell is Git Bash; `core.autocrlf` is `input` | one shell and one line-ending setting for the whole class |

## 4. Commit, archive, and submit

The check is also a rehearsal of how every homework is submitted:

```bash
git add check_output.txt check_notebook.ipynb
git commit -m "Setup check passes on my laptop: duckdb 1.5.5, 525,461 rows"
git archive --format=zip -o ../setup-submission.zip HEAD
```

Upload **`setup-submission.zip`** (it is in the folder above the project) to the Moodle **Setup verification** slot.
In the text box, write three things:

1. the contents of `check_output.txt`, pasted;
2. whether you took DS1;
3. roughly how long setup took you, from the first install to this upload.

If you did not take DS1, or setup failed or took you more than an hour, **book a supported 30-minute rehearsal with a TA** in the week of 28 September, on Moodle. It is there to help, not to test you.

## 5. Rules that save you an hour

- **Windows: never type bare `python` in Git Bash.** On some setups it hangs with no error. Always give it something
  to run: `python --version`, `uv run python check.py`.
- **Notebooks run on the project's own kernel.** Open the project **folder** in VS Code, pick the `.venv` interpreter
  in the kernel picker, and prove it with `import sys; sys.executable`. A global Python or an Anaconda kernel is a
  different environment, with whatever DuckDB it happens to have, or none.
- **Every course project is run from its own folder**, the one containing `pyproject.toml`. Every path in the course,
  `'data/raw/…'` included, is written from there.

## 6. If it still does not work

Submit the failing output anyway, and say in the Moodle text box what you tried: it tells us exactly what to help
with. Then book a supported 30-minute rehearsal with a TA in the week of 28 September, on Moodle. The most common fixes: close and reopen the terminal after installing `uv`; on Windows, the Microsoft Store
`python` alias (the check says so); running from a folder inside the project instead of the project folder; an
Anaconda Python selected as the notebook's kernel.
