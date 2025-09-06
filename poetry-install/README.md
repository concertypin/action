# Setup Poetry Environment

This GitHub Action sets up a Python environment with Poetry, configures a local virtual environment, caches dependencies, and installs them.

It's designed to be a reusable workflow for any Python project using Poetry.

## Usage

To use this action in your workflow, you need to reference this repository.

Here's an example of a workflow that checks, lints, and tests a Python project:

```yaml
name: CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Poetry Environment
        uses: concertypin/action/poetry-install@your-hash
        with:
          python-version: '3.12'
          install-args: '--with dev' # Or whatever args you need, defaults to '--without dev'

      - name: Run checks
        run: poetry run black . --check

      - name: Run linter
        run: poetry run ruff check .
      
      - name: Run tests
        run: poetry run pytest
```
## Inputs

| Name                | Description                               | Default           | Required |
| ------------------- | ----------------------------------------- | ----------------- | -------- |
| `python-version`    | The Python version to use.                | `'3.12'`          | `true`   |
| `working-directory` | The directory where `poetry` commands run.| `'.'`             | `false`  |
| `install-args`      | Extra arguments for the `poetry install`. | `'--without dev'` | `false`  |

