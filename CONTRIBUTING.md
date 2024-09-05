# Contributing to r4py

Welcome! We're happy to have you here. Thank you in advance for your contribution to r4py.

## Local development environment (Linux, MacOS, Windows)

### Setup

- Install `uv`.
- Create virtual environment with `uv venv`, `source .venv/bin/activate`, `uv pip install --require-hashes -r requirements.txt`.

### Development

- Fork this project.
- Get familiar with [mkdocs.org](https://www.mkdocs.org) and [Material for MkDocs (mkdocs-material)](https://squidfunk.github.io/mkdocs-material/).
- Add/change/remove content in `docs`.
- Serve the website locally using `mkdocs serve`, visit `http://127.0.0.1:8000/` and check if your additions/changes/removes are ok.
- Push changes to your GitHub repo fork,
- Create a PR.

## Remote development environment (Browser -> GitPod)

## Setup

- Login into GitPod via GitHub (it's free)
- Fork this project.
- In `README.md` adjust `[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/fkromer/r4py)` to `[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/<your-username>/r4py)`.
- Open the project in GitPod via "Open in GitPod" button in `README.md`.

### Development

Same as in `Local development environment`.

## Build dependencies

### Adding mkdocs features

If you want to add `mkdocs` features which require adding Python package dependencies:

- Add the Python package to the venv with `uv add <your-mkdocs-package-of-choice>`. This will update the `uv.lock` file.
- Update dependencies in `requirements.txt` with `uv pip compile --generate-hashes pyproject.toml -o requirements.txt`.
- Commit `uv.lock` as well as `requirements.txt`.
