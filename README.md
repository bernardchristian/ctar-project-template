# ctar-project-template

This repository is the **Python version of a barebones project template**. It is meant to be small enough to understand quickly, but structured enough to grow into a real project.

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
.gitattributes
.gitignore
.python-version
_secrets.env
LICENSE
Makefile
README.md
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
  _quarto.yml
  paper.qmd
  presentation.qmd
  references.bib
docker_devcontainer_prompt.md
makefile_prompt.md
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

- `data/external/`: files that come from outside the repository and are kept as source material
- `data/pulled/`: raw data written by a pull step
- `data/generated/`: prepared datasets created from raw or external inputs

In this template, the pull step uses the built-in `mtcars` dataset that ships with `plotnine`, so `data/external/` starts empty. The folder is still there so you can swap in your own real project data later without changing the overall structure.

`data/pulled/` and `data/generated/` are listed in `.gitignore`, and so is everything in `output/` except the rendered PDFs. All of it can be recreated by running the workflow. If your project uses licensed data (for example from WRDS), keep your repository private.

## References

The paper also includes a minimal bibliography workflow. The bibliography file lives at:

- `doc/references.bib`

and `doc/paper.qmd` cites at least one reference from that file. That way you can already see the basic citation pattern in a working template rather than adding it later from scratch.

## Credentials

If your project pulls licensed data, for example Compustat from WRDS, your code needs a username and a password. Never write them into a script. Instead, copy the template file in the repository root:

```bash
cp _secrets.env secrets.env
```

Then open `secrets.env` and replace the placeholders with your credentials. `secrets.env` is listed in `.gitignore`, so it is never committed. In Python, you can read the values with:

```python
from pathlib import Path

secrets = dict(
    line.split("=", 1)
    for line in Path("secrets.env").read_text().splitlines()
    if "=" in line and not line.startswith("#")
)
secrets["WRDS_USERNAME"]
```

The `mtcars` example does not need credentials. The project environment already includes the `duckdb` package, which you need to pull data from WRDS via DuckDB.

## Recommended Setup Paths

There are three ways to work with this repo:

1. **GitHub Codespaces**
   This is the recommended path. Nothing to install.
2. **VS Code + Docker Desktop (Dev Containers)**
   This is the recommended local path, also on Windows.
3. **Fully local install**
   This is possible, but not recommended.

### 1. GitHub Codespaces

1. Use this template on GitHub to create your own repository.
2. Open your repository in Codespaces.
3. Wait for the container to finish building.
4. Wait for the post-create step to finish running `uv sync`.
5. In the Codespaces terminal, run:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
gh auth login
```

Then run:

```bash
make
```

The project virtual environment lives at `.venv/`. Codespaces detects it automatically and uses it as the Python interpreter. The interpreter itself is managed by `uv`, which downloads the Python version pinned in `.python-version`.

Codespaces stop automatically after a period of inactivity, but the free monthly hours are limited. Stop your codespace when you are done (`Code -> Codespaces -> ... -> Stop codespace`).

### 2. VS Code + Docker Desktop (Dev Containers)

1. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/) and start it. On Windows, it runs on WSL 2.
2. Install [VS Code](https://code.visualstudio.com/) and its **Dev Containers** extension (`ms-vscode-remote.remote-containers`).
3. Clone your repository and open its folder in VS Code.
4. When VS Code asks, click **Reopen in Container** (or press `Ctrl+Shift+P` and run `Dev Containers: Reopen in Container`). The first build takes 5 to 15 minutes. Later starts take seconds.
5. Wait for the post-create step to finish running `uv sync`.
6. Open a terminal with `Terminal -> New Terminal`. It runs inside the container. Then run:

```bash
make
```

The outputs appear in the `output/` folder of your local repository. The container keeps the project virtual environment in `.venv/` (in a Docker volume, separate from your local files) and configures VS Code to use it automatically.

If Git inside the container reports "dubious ownership", run `git config --global --add safe.directory "$(pwd)"` in the container terminal.

To leave the container, press `Ctrl+Shift+P` and run `Dev Containers: Reopen Folder Locally`. After you change the `Dockerfile` or `devcontainer.json`, run `Dev Containers: Rebuild Container`.

### 3. Fully Local Install

You can also run the project outside containers, but this is **not recommended** unless you are comfortable managing the stack yourself:

- `uv`
- Quarto
- a LaTeX installation capable of rendering PDFs
- GNU Make
- Git and optionally GitHub CLI

If you choose this route, run:

```bash
uv sync --managed-python
source .venv/bin/activate
make
```

## Main Project Command

Run the whole project from the repository root with:

```bash
make
```

The Makefile runs the full pipeline in order:

1. `code/python/pull_data.py`
2. `code/python/prep_data.py`
3. `code/python/run_analysis.py`
4. `doc/paper.qmd`
5. `doc/presentation.qmd`

`make` only reruns the steps whose inputs have changed. Editing `doc/paper.qmd`, for example, only re-renders the paper. `make clean` deletes all pulled and generated data and all outputs, so that the next `make` runs everything from scratch.

To add a Python package, run `uv add <package>`. This updates `pyproject.toml` and `uv.lock`, which you then commit.

## Outputs

The main analytical output is:

- `output/results.pkl`

The final paper and slides are written to:

- `output/paper.pdf`
- `output/presentation.pdf`

That paper imports one saved descriptive table and the prepared data for one figure from the results bundle. The analytical objects are prepared first, then rendered in the paper.

The two PDFs are committed to the repository, so commit and push them after each `make` run that changes them. This is how your final paper and slides are submitted. Everything else in `output/`, including `results.pkl`, stays local.

## The Paper

The paper source lives in:

- `doc/paper.qmd`

It is formatted as a small article-style paper so the repository already feels like a miniature research template rather than a single script with a report attached at the end.
The current template shows one descriptive table, one figure, and one bibliography entry so the reporting workflow stays visible without becoming crowded.

The slide deck in `doc/presentation.qmd` reads the same results bundle and shows the same table and figure as beamer slides.

## AI Prompts for Common Tasks

Two ready-made prompts are included to help you work with the project configuration using an LLM assistant.

- **`makefile_prompt.md`** — use this if you want to understand how the `Makefile` works or need help adapting it to your own pipeline.
- **`docker_devcontainer_prompt.md`** — use this if you run into errors with the `.devcontainer/` setup or want to understand how the `Dockerfile` and `devcontainer.json` interact.

In each file, replace the text inside the `{{ }}` blocks with your own input, then paste the whole prompt into an LLM of your choice.

## Container Notes

Both container paths (Codespaces and VS Code Dev Containers) provide:

- `uv`
- `git`
- `gh`
- `make`
- Quarto
- TinyTeX with the LaTeX packages needed by the paper and the slides

In both container paths, `uv` downloads and manages the Python interpreter pinned for the project and installs the packages listed in `pyproject.toml` and `uv.lock`, including `duckdb` for pulling data from WRDS. This keeps the working environment consistent across students without baking the project Python version into the base image.

If Quarto reports a missing LaTeX package when you render your own paper, install that package on demand with `tlmgr install <package>`, and add it to the `tlmgr install` list in `.devcontainer/Dockerfile` so that it is there after the next rebuild.

The `.gitattributes` file keeps Linux line endings in all text files, also when you clone the repository on Windows. This avoids errors when the files are used inside the Linux container.

## Credits

This template is based on [fedossa/rct-project-template](https://github.com/fedossa/rct-project-template), a reduced version of the TRR 266 template for reproducible empirical accounting research [trr266/treat](https://github.com/trr266/treat). See `LICENSE`.
