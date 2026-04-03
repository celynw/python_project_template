# python-project-template

A personal [Copier](https://copier.readthedocs.io/) template for new Python projects.

Generates a `uv`-based Python project with opinionated defaults for linting, type checking, testing, and pre-commit hooks - all configured in a single `pyproject.toml`.

| Tool                                                                                  | Role                                           |
| ------------------------------------------------------------------------------------- | ---------------------------------------------- |
| [uv](https://docs.astral.sh/uv/)                                                      | Package & environment management               |
| [Ruff](https://docs.astral.sh/ruff/)                                                  | Linting & formatting (`select = ["ALL"]`)      |
| [mypy](https://mypy.readthedocs.io/) (`strict`)                                       | Type checking                                  |
| [pytest](https://docs.pytest.org/) + [pytest-cov](https://pytest-cov.readthedocs.io/) | Testing & coverage                             |
| [markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2)                  | Markdown linting                               |
| [prek](https://github.com/j178/prek)                                                  | Pre-commit hooks                               |
| [Pyright/Pylance](https://github.com/microsoft/pyright)                               | IDE completions only (`typeCheckingMode: off`) |

```text
my_project/
├── pyproject.toml       # ruff, mypy, pytest, coverage - all in one place
├── pyrightconfig.json   # Pylance completions only, type errors deferred to mypy
├── prek.toml            # pre-commit hooks
├── .markdownlint.yaml   # markdownlint config
├── .gitignore
├── src/
│   └── my_project/
│       └── __init__.py
└── test/
    └── __init__.py
```

## Prerequisites

```bash
uv tool install copier
uv tool install prek
```

## Creating a new project

```bash
copier copy git@github.com:celynw/python-project-template.git /path/to/new-project
```

You'll be prompted for:

| Question               | Default                          |
| ---------------------- | -------------------------------- |
| Project name           | _(required)_                     |
| Package name           | derived from project name        |
| Author name            | Celyn Walters                    |
| Author email           | _(my GitHub no-reply address)_   |
| Minimum Python version | `3.14`                           |

Then set up the project:

```bash
cd /path/to/new-project
uv sync        # Create .venv and install dev dependencies
prek install   # Install pre-commit hooks
```

## Updating an existing project

If this template improves, pull the changes into any project that was generated from it:

```bash
cd /path/to/existing-project
copier update
```

Copier will show a diff and let you resolve any conflicts.

## What's configured

### Ruff

`select = ["ALL"]` with a minimal ignore list targeting genuine conflicts and noise:

* `D203`/`D212` - conflicting docstring rules (numpy convention used instead)
* `E203` - slice whitespace (conflicts with black-style formatting)
* `ISC001` - implicit string concatenation (conflicts with ruff formatter)
* `ERA001` - commented-out code (too noisy in practice)
* `CPY001` - copyright headers

Test files additionally ignore `S101` (assert), `PLR2004` (magic values), `FBT` (boolean args), and docstring rules.

### mypy

`strict = true`.
Add per-library overrides in `pyproject.toml` as needed:

```toml
[[tool.mypy.overrides]]
module = "some_untyped_lib.*"
ignore_missing_imports = true
```

### pytest

Runs from `test/`, with `src/` on the Python path (no install needed for local dev).
Coverage report is printed to terminal and written to `htmlcov/`.

### markdownlint

Configured via `.markdownlint.yaml`.
Notable choices:

* MD013 disabled - line length is not enforced
* MD049/MD050 - `_underscores_` for emphasis, `**asterisks**` for strong
* MD054 - inline links only (`[text](url)`)
* MD059 - link text must be descriptive (no "click here")

### Pre-commit hooks

| Hook                                       | Purpose                                    |
| ------------------------------------------ | ------------------------------------------ |
| `ruff` + `ruff-format`                     | Lint and format on commit                  |
| `mypy`                                     | Type-check on commit                       |
| `markdownlint-cli2`                        | Lint markdown on commit                    |
| `uv-lock`                                  | Ensure `uv.lock` stays in sync             |
| `check-toml`, `check-yaml`, `check-json`   | Validate config files                      |
| `end-of-file-fixer`, `trailing-whitespace` | Whitespace hygiene                         |
| `mixed-line-ending`                        | Enforce LF                                 |
| `check-added-large-files`                  | Prevent accidental large file commits      |
