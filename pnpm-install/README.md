# Setup pnpm Environment

This GitHub Action sets up a pnpm environment, configures Node.js, caches dependencies, and handles Playwright browsers if needed.

It is designed to be a fast, reusable workflow for pnpm-based projects, with built-in support for monorepos and smart caching strategies.

> [!NOTE]
> This action officially supports `ubuntu-latest` only. (Unless you are a [*Gigachad*](#inputs)).

## Usage

To use this action in your workflow, reference this repository in your step.

Here's an example of a workflow that installs dependencies (including Playwright) and runs tests:

```yaml
name: CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup pnpm Environment
        uses: concertypin/action/pnpm-install@your-hash
        with:
          # Reads version from package.json > engines by default
          # node-version: '20' 
          install-playwright-deps: 'true'

      - name: Run checks
        run: pnpm run lint

      - name: Run Playwright tests
        run: pnpm exec playwright test
```

## Inputs

| Name | Description | Default | Required |
| :--- | :--- | :--- | :--- |
| `node-version` | Version of Node.js to use. Preferred to be set in `package.json` engines. | `undefined` | `false` |
| `working-directory` | The directory where `pnpm` commands run. Should contain `package.json`. | `'.'` | `false` |
| `install-playwright-deps` | Whether to install Playwright browsers and OS dependencies. | `'false'` | `false` |
| `repo-root` | Root of the repository. Used to locate `pnpm-lock.yaml` for caching. | `'.'` | `false` |
| `gigachad` | **Overrides runner OS check.** 스탑 오버 띵킹, 만삣삐. For gigachads only. | `'false'` | `false` |

> [!NOTE]
> `node-version` or `engines` field in `package.json` should be set to ensure the correct Node.js version is used.
> It's recommended to set `engines` in `package.json` for better consistency across environments.

---

## Notes

- **OS Support:** Officially supports `ubuntu-latest`.
- **Caching:** Automatically caches `node_modules` (via pnpm) and Playwright browsers to speed up your CI/CD pipelines.

- **Node.js Version:** If `node-version` is not provided, the action will automatically detect the required version from the `engines` field in your `package.json`.
