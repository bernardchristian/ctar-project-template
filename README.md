# ctar-project-template (Python)

This branch is the **Python version of a barebones project template** for Current Topics in Accounting Research (CTAR). It is small enough to understand quickly, but structured like a real empirical project. The example uses the `mtcars` dataset; you replace it with your own data and analysis.

The R version of the template lives on the branch `main`.

## How the Workflow Moves

| Step | Script | Writes |
|---|---|---|
| 1. Pull the data | `code/python/pull_data.py` | `data/pulled/` |
| 2. Prepare the data | `code/python/prep_data.py` | `data/generated/` |
| 3. Run the analysis | `code/python/run_analysis.py` | `output/results.pkl` |
| 4. Write the paper and slides | `doc/paper.qmd`, `doc/presentation.qmd` | `output/paper.pdf`, `output/presentation.pdf` |

The paper and the slides do not rerun the analysis: they read the saved results in `output/results.pkl`. The `Makefile` runs all steps in order and only reruns the steps whose inputs changed:

```bash
make        # run the whole workflow
make clean  # delete all generated data and outputs
```

## Project Structure

```text
.devcontainer/          the container: Quarto, LaTeX, uv (Dockerfile, devcontainer.json)
pyproject.toml, uv.lock the Python packages and their exact versions
code/python/            the three Python scripts
data/external/          data you add by hand (e.g., a manual download)
data/pulled/            raw data written by pull_data.py (not committed)
data/generated/         prepared data written by prep_data.py (not committed)
doc/                    paper.qmd, presentation.qmd, references.bib
output/                 results and the rendered PDFs (only the PDFs are committed)
Makefile                runs the workflow
_secrets.env            template for your credentials file
```

## Getting Started

1. Click **Use this template** → **Create a new repository**, switch on **Include all branches**, and make the repository **Private**. Then, in your repository, set the default branch to `python` (**Settings** → **Default branch**).
2. Open your repository in one of three ways:
   - **Dev Container (recommended).** Install [VS Code](https://code.visualstudio.com/), [Git](https://git-scm.com/), [Docker Desktop](https://www.docker.com/products/docker-desktop/), and the VS Code extension **Dev Containers**. Clone your repository in VS Code (`Ctrl+Shift+P` → **Git: Clone**) and run **Dev Containers: Reopen in Container**. The first build takes 10 to 20 minutes and installs the Python packages with `uv sync`.
   - **GitHub Codespaces (easiest, nothing to install).** On your repository page, click **Code** → **Codespaces** → **Create codespace on python**. Free hours are limited, so stop your codespace when you are done (**Code** → **Codespaces** → **...** → **Stop codespace**).
   - **Local installation (not recommended).** Install [uv](https://docs.astral.sh/uv/), Quarto, TinyTeX, and `make` yourself, then run `uv sync` in the repository root.
3. Open a terminal (**Terminal** → **New Terminal**) and run `make`. The PDFs appear in `output/`.

## Adding Python Packages

The environment only includes the packages of the `mtcars` example. Add the packages your project needs with `uv`, for example:

```bash
uv add duckdb pyfixest
```

`uv` installs them right away (no rebuild needed) and records them in `pyproject.toml` and `uv.lock`. Commit both files, so that your workflow runs on every computer.

## Credentials

If you pull licensed data, for example from WRDS, never write your username and password into a script. Copy the template and add your credentials there:

```bash
cp _secrets.env secrets.env
```

`secrets.env` is listed in `.gitignore` and is never committed. If your project uses licensed data, keep your repository private.

## AI Prompts

- `makefile_prompt.md` helps you understand and adapt the `Makefile`.
- `docker_devcontainer_prompt.md` helps with questions and errors around `.devcontainer/`.

Replace the text inside the `{{ }}` blocks and paste the prompt into an LLM of your choice.

## Credits

This template is based on [fedossa/rct-project-template](https://github.com/fedossa/rct-project-template), a reduced version of the TRR 266 template for reproducible empirical accounting research [trr266/treat](https://github.com/trr266/treat). See `LICENSE`.
