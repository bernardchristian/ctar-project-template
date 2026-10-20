# ctar-project-template (Python)

This branch is the **Python version of a barebones project template** for Current Topics in Accounting Research (CTAR). It is meant to be small enough to understand quickly, but structured enough to grow into a real project. The R version lives on the branch `main`.

The main idea is simple:

- data are pulled into `data/pulled/`
- data are prepared into `data/generated/`
- analysis writes a serialized results bundle to `output/`
- the paper and the slides in `doc/` read those saved results

So even though the example uses the tiny `mtcars` dataset, the workflow is already organized like a real empirical project.

## What You Are Looking At

This repository gives you a minimal project skeleton with four visible stages:

1. `code/python/pull_data.py`
2. `code/python/prep_data.py`
3. `code/python/run_analysis.py`
4. `doc/paper.qmd` (and `doc/presentation.qmd`)

The point is not the `mtcars` analysis itself. The point is to give you a clean starting structure that you can keep extending for your own work.

If you later look at `trr266/treat`, you will see the same broad movement in a richer and more elaborate form.

## Project Structure

```text
.devcontainer/
  Dockerfile
  devcontainer.json
Makefile
README.md
_secrets.env
pyproject.toml
uv.lock
code/python/pull_data.py
code/python/prep_data.py
code/python/run_analysis.py
data/
  external/
  pulled/
  generated/
  data_readme.md
doc/
  paper.qmd
  presentation.qmd
  references.bib
output/
```

## How The Workflow Moves

The workflow is intentionally explicit:

1. `pull_data.py` creates a raw object in `data/pulled/`
2. `prep_data.py` reads that raw object and creates a prepared analysis dataset in `data/generated/`
3. `run_analysis.py` reads the prepared dataset and writes a serialized `.pkl` results bundle to `output/`
4. `doc/paper.qmd` and `doc/presentation.qmd` read that `.pkl` bundle and render the paper and the slides

The paper does **not** rerun the full analysis pipeline internally. It consumes prepared results from `output/`.

## The `data/` Folder

The `data/` folder keeps the same conceptual separation used in `treat`:

- `data/external/`: files that come from outside the repo and are kept as source material
- `data/pulled/`: raw data written by a pull step
- `data/generated/`: prepared datasets created from raw or external inputs

In this template, the pull step uses the `mtcars` dataset that ships with `plotnine`, so `data/external/` starts empty. The folder is still there so you can swap in your own real project data later without changing the overall structure.

`data/pulled/`, `data/generated/`, and everything in `output/` except the rendered PDFs are listed in `.gitignore`, because they can be recreated by running the workflow. If you pull licensed data, for example from WRDS, keep your credentials out of your code: copy `_secrets.env` to `secrets.env` and add your credentials there. `secrets.env` is never committed.

## References

The paper also includes a minimal bibliography workflow. The bibliography file lives at:

- `doc/references.bib`

and `doc/paper.qmd` cites at least one reference from that file. That way you can already see the basic citation pattern in a working template rather than adding it later from scratch.

## Recommended Setup Paths

There are three ways to work with this repo:

1. **VS Code + Docker Desktop (Dev Container)**
   This is the recommended path.
2. **GitHub Codespaces**
   This is the easiest path. Nothing to install.
3. **Fully local install**
   This is possible, but not recommended.

In all three cases, first use this template on GitHub to create your own **private** repository. Switch on **Include all branches**, so that this `python` branch is copied, and then set the default branch of your repository to `python` (**Settings** -> **Default branch**).

### 1. VS Code + Docker Desktop (Dev Container)

1. Install [VS Code](https://code.visualstudio.com/), [Git](https://git-scm.com/), and [Docker Desktop](https://www.docker.com/products/docker-desktop/). Start Docker Desktop and wait until it reports **Engine running**.
2. In VS Code, install the **Dev Containers** extension (publisher Microsoft).
3. Clone your repository: press `Ctrl+Shift+P`, run `Git: Clone`, choose **Clone from GitHub**, and select your repository.
4. Press `Ctrl+Shift+P` and run `Dev Containers: Reopen in Container`. Confirm that you trust the folder.
5. Wait for the container to finish building. The first build takes 10 to 20 minutes and installs the Python packages with `uv sync`. Later starts take seconds.
6. Open a terminal with `Terminal -> New Terminal` and run:

```bash
make
```

### 2. GitHub Codespaces

1. On your repository page, click **Code** -> **Codespaces** -> **Create codespace on python**.
2. Wait for the container to finish building. VS Code then opens in your browser.
3. Open a terminal with `Terminal -> New Terminal` and run:

```bash
make
```

Codespaces are free for a limited number of hours per month. Stop your codespace when you are done: **Code** -> **Codespaces** -> **...** -> **Stop codespace**.

### 3. Fully Local Install

You can also run the project outside containers, but this is **not recommended** unless you are comfortable managing the stack yourself:

- [uv](https://docs.astral.sh/uv/), which installs Python and the packages from `uv.lock` with `uv sync`
- Quarto
- TinyTeX or another LaTeX installation
- GNU Make
- Git

If you choose this route, the project command is still:

```bash
make
```

## Main Project Command

Run the whole project from the repository root with:

```bash
make
```

The Makefile runs the full pipeline in order, each step with `uv run`:

1. `code/python/pull_data.py`
2. `code/python/prep_data.py`
3. `code/python/run_analysis.py`
4. `doc/paper.qmd`
5. `doc/presentation.qmd`

`make` only reruns the steps whose inputs have changed. `make clean` deletes all generated data and outputs, so that the next `make` runs everything from scratch.

## Outputs

The main analytical output is:

- `output/results.pkl`

The final paper and slides are written to:

- `output/paper.pdf`
- `output/presentation.pdf`

The two PDFs are committed to the repository: commit and push them after each `make` run that changes them.

## The Paper

The paper source lives in:

- `doc/paper.qmd`

It is formatted as a small article-style paper so the repository already feels like a miniature research template rather than a single script with a report attached at the end.
The current template shows one descriptive table, one figure, and one bibliography entry so the reporting workflow stays visible without becoming crowded. The slides in `doc/presentation.qmd` read the same results bundle and show the same table and figure.

## AI Prompts for Common Tasks

Two ready-made prompts are included to help you work with the project configuration using an LLM assistant.

- **`makefile_prompt.md`** — use this if you want to understand how the `Makefile` works or need help adapting it to your own pipeline.
- **`docker_devcontainer_prompt.md`** — use this if you run into errors with the `.devcontainer/` setup or want to understand how the `Dockerfile` and `devcontainer.json` interact.

In each file, replace the text inside the `{{ }}` blocks with your own input, then paste the whole prompt into an LLM of your choice.

## Container Notes

The Dev Container and Codespaces provide:

- Python and the packages from `uv.lock`, managed by `uv`
- `git`
- `gh`
- `make`
- Quarto
- TinyTeX

This keeps the working environment consistent across students.

When your project needs more Python packages, add them with `uv`, for example:

```bash
uv add duckdb pyfixest
```

`uv` installs them right away (no container rebuild needed) and records them in `pyproject.toml` and `uv.lock`. Commit both files.

## Credits

This template is based on [fedossa/rct-project-template](https://github.com/fedossa/rct-project-template), a reduced version of [trr266/treat](https://github.com/trr266/treat). See `LICENSE`.
