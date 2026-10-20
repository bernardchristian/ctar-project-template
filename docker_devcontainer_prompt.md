I need help understanding and updating the two configuration files that define the development environment for my research project: a `Dockerfile` and a `devcontainer.json`. These files live in the `.devcontainer/` folder and together define the software environment that runs my project — whether in GitHub Codespaces or local Docker.

My project is an R project that pulls, prepares, and analyzes data using a Make-driven pipeline and renders a final paper with Quarto.

Here are my current files:

**Dockerfile:**
```
FROM rocker/rstudio:4.5

ENV DEBIAN_FRONTEND=noninteractive TZ=UTC LANG=C.UTF-8

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      git \
      gh \
      make \
      wget \
      xz-utils \
      ca-certificates \
      curl \
      libcurl4-openssl-dev \
      libssl-dev \
      libxml2-dev \
 && rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/*

# tinytex installs LaTeX in the next step
RUN install2.r --error --skipinstalled tinytex \
 && rm -rf /tmp/downloaded_packages

RUN Rscript -e "tinytex::install_tinytex(dir = '/opt/TinyTeX', force = TRUE)" \
 && ln -sf /opt/TinyTeX/bin/*/* /usr/local/bin/

# Pre-install LaTeX packages needed by the paper and presentation
RUN tlmgr option repository https://ftp.fau.de/ctan/systems/texlive/tlnet \
 && tlmgr update --self \
 && tlmgr install \
      adjustbox \
      beamer \
      booktabs \
      caption \
      csquotes \
      endfloat \
      footmisc \
      geometry \
      latexmk \
      setspace \
      threeparttable \
 && test -n "$(kpsewhich adjustbox.sty)" \
 && test -n "$(kpsewhich beamer.cls)" \
 && test -n "$(kpsewhich booktabs.sty)" \
 && test -n "$(kpsewhich caption.sty)" \
 && test -n "$(kpsewhich csquotes.sty)" \
 && test -n "$(kpsewhich endfloat.sty)" \
 && test -n "$(kpsewhich footmisc.sty)" \
 && test -n "$(kpsewhich geometry.sty)" \
 && test -n "$(kpsewhich setspace.sty)" \
 && test -n "$(kpsewhich threeparttable.sty)"

RUN set -eux; \
  QUARTO_VERSION=1.6.40; \
  ARCH="$(dpkg --print-architecture)"; \
  case "$ARCH" in \
    amd64) QA=linux-amd64 ;; \
    arm64) QA=linux-arm64 ;; \
    *) echo "Unsupported arch: $ARCH" >&2; exit 1 ;; \
  esac; \
  wget -O /tmp/quarto.deb \
    "https://github.com/quarto-dev/quarto-cli/releases/download/v${QUARTO_VERSION}/quarto-${QUARTO_VERSION}-${QA}.deb"; \
  dpkg -i /tmp/quarto.deb; \
  rm /tmp/quarto.deb

RUN echo "rstudio ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/rstudio \
 && chmod 0440 /etc/sudoers.d/rstudio

RUN mkdir -p /workspaces \
 && chown -R rstudio:rstudio /home/rstudio /opt/TinyTeX /workspaces

# --- R packages of the project -------------------------------------------------
# This list holds the packages of the mtcars example. Add each package your
# project needs as a new line, written like the lines below: the package name
# followed by " \". Then run "Dev Containers: Rebuild Container". Because this
# is the last step, a rebuild only reinstalls these packages (a minute or two).
RUN install2.r --error --skipinstalled \
      ggplot2 \
      knitr \
      rmarkdown \
 && rm -rf /tmp/downloaded_packages

WORKDIR /workspaces
```

**devcontainer.json:**
```
{
  "name": "ctar-project-template",
  "build": { "dockerfile": "Dockerfile" },
  "overrideCommand": false,
  "workspaceFolder": "/workspaces/${localWorkspaceFolderBasename}",
  "remoteUser": "rstudio",
  "containerEnv": {
    "PASSWORD": "rstudio"
  },
  "forwardPorts": [8787],
  "portsAttributes": {
    "8787": {
      "label": "RStudio Server",
      "onAutoForward": "openPreview"
    }
  },
  "hostRequirements": {
    "cpus": 2,
    "memory": "4gb",
    "storage": "16gb"
  },
  "postCreateCommand": "sudo chown -R rstudio:rstudio ${containerWorkspaceFolder} && git config --global --add safe.directory ${containerWorkspaceFolder}",
  "customizations": {
    "vscode": {
      "extensions": [
        "REditorSupport.r",
        "quarto.quarto",
        "GitHub.vscode-github-actions"
      ]
    }
  }
}
```

Here is an error message that I currently receive when running `make all` inside the terminal of the development container. 

{{

YOUR INPUT:

Copy and Paste your error message from running `make all` here.

}}

Please help me with the following:

1. Explain what both files do in plain language. After this general overview, please focus your discussions on those parts of the files that might be related to the error I am experiencing
2. Explain what my error message above means and how I should modify the files above to fix it. 
