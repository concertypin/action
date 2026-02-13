# Show Coverage Report

This GitHub Action displays Vitest coverage report status directly on your GitHub commits and Pull Requests.

It is designed to provide immediate feedback on code quality by checking coverage percentages against your defined thresholds and reporting them via the GitHub Commit Status API.

> [!NOTE]
> This action requires a coverage summary file in JSON format. Ensure your Vitest configuration includes the 'json-summary' reporter.

## Usage

To use this action, reference it in your workflow after your test execution step.

Example Workflow Sequence:
1. Setup Environment: Initialize Node.js and install dependencies.
2. Run Tests with Coverage: Execute 'vitest --coverage --coverage.reporter=json-summary'.
3. Show Coverage: Use this action to parse the resulting JSON and update the commit status.

By default, the action looks for the summary file at './coverage/coverage-summary.json'. You can customize this path and the success threshold using the inputs defined below.

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
    permissions:
      contents: read
      pull-requests: write
      statuses: write
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      # ...

      - name: Run test
        run: pnpm vitest run --coverage.enabled --coverage.reporter json-summary

      - name: Report
        if: always()
        uses: concertypin/action/report-vitest-coverage@your-hash
        with:
          threshold: 80

```


## Inputs

| Name        | Description                                                     | Default                              | Required |
| :---------- | :-------------------------------------------------------------- | :----------------------------------- | :------- |
| `json-file` | Path to the coverage report JSON (generated with json-summary). | `'./coverage/coverage-summary.json'` | `false`  |
| `threshold` | Minimum percentage for line coverage required to pass.          | `'75'`                               | `false`  |
| `title`     | The display title for the GitHub commit status context.         | `'Vitest Coverage'`                  | `false`  |

> [!NOTE]
> The 'threshold' value is compared against 'Lines' coverage. If the coverage is lower than this value, the commit status will be marked as 'failure', while job itself will be marked as success without blocking other steps.

## Prerequisites

To ensure this action works correctly, your Vitest config(usually `vitest.config.ts` or `vite.config.ts`) should be configured as follows:

Coverage Configuration:
- Set `test.coverage.provider` to `v8` or `istanbul`.
- Include `json-summary` or `json` in the 'reporter' array.

Example:
```ts
defineConfig({
    test: {
        coverage: {
            reporter: ['text', 'json-summary'],
            reportOnFailure: true // Not required, but recommended.
        },
    },
    //...
})
```

## Notes

- **Commit Status:** The action creates a status check on the current commit. This allows you to see coverage metrics at a glance in the PR "Checks" section.
- **Metrics Displayed:** The description field of the status will show percentages for Lines, Statements, Functions, and Branches.
- **Workflow Links:** Clicking "Details" on the GitHub status will take you directly to the relevant GitHub Actions run.