---
title: 'GitHub Action'
date: 2026-10-18T19:30:08+10:00
draft: false
weight: 16
group: 'Publishing results'
summary: GitHub Action for publishing test results, coverage, and attachments to Projektor from GitHub Actions workflows
---

The [Projektor GitHub Action](https://github.com/craigatk/projektor-action) publishes your test results
to the Projektor server from a GitHub Actions workflow. It's built on the [Node script](/docs/node-script/),
so it supports the same test result, coverage, attachment, and performance formats, but you don't need to
add any dependencies to your project. That makes it a good fit for projects in any language, as long as
the tests write JUnit XML results.

## Usage

Add a step after your tests run that publishes the results:

```yaml
- name: Run tests
  run: npm test

- name: Publish results to Projektor
  if: always()
  uses: craigatk/projektor-action@v15.1.0
  with:
    server-url: https://projektor.example.com
    results: test-results/*.xml
    coverage: coverage/clover.xml
```

`if: always()` makes the step run even when the tests fail - which is when you most want to see the
results.

To publish multiple file patterns, put each one on its own line:

```yaml
  with:
    server-url: https://projektor.example.com
    results: |
      ui/test-results/*.xml
      ui/cypress/results/*.xml
    attachments: |
      ui/cypress/screenshots/**/*.png
      ui/cypress/videos/*.mp4
```

### Using a config file

The action also reads a [Node script config file](/docs/node-script/#usage), `projektor.json` in the root of
your repository by default. Use a config file for options the action doesn't have its own input for,
such as `codeQuality`, `exitWithFailure`, or `gitMainBranchNames`:

```yaml
- uses: craigatk/projektor-action@v15.1.0
  with:
    config-file: projektor.json
```

When the config file exists, the action takes its settings from the config file and ignores the other
inputs such as `server-url` and `results` (except `token`). So use either a config file or the individual
inputs, not both - and if you use the inputs, make sure there's no `projektor.json` in the root of your repository.

### Publish token

If your Projektor server [requires a publish token](/docs/server/#authentication), store the token as a
GitHub Actions secret and pass it with the `token` input:

```yaml
- uses: craigatk/projektor-action@v15.1.0
  with:
    server-url: https://projektor.example.com
    results: test-results/*.xml
    token: ${{ secrets.PROJEKTOR_TOKEN }}
```

### Using the report link

The action sets the `report-url` output to the Projektor report's URL, so later steps can use it -
for example to include the link in a notification:

```yaml
- name: Publish results to Projektor
  id: projektor
  if: always()
  uses: craigatk/projektor-action@v15.1.0
  with:
    server-url: https://projektor.example.com
    results: test-results/*.xml

- name: Show report link
  if: always()
  run: echo "Projektor report - ${{ steps.projektor.outputs.report-url }}"
```

## Inputs

| Input                 | Default          | Description |
|-----------------------|------------------|-------------|
| `server-url`          |                  | Projektor server URL to publish results to. Required unless set in the config file. |
| `results`             |                  | Paths to the test results XML files, one per line |
| `coverage`            |                  | Paths to [code coverage](/docs/code-coverage/) files, one per line |
| `attachments`         |                  | Paths to files to attach to the test report, such as screenshots, one per line |
| `performance`         |                  | Paths to [performance test](/docs/performance-test/) results files, one per line |
| `project-name`        |                  | Project name, when publishing multiple Projektor reports from the same repo |
| `token`               |                  | Token for publishing to a Projektor server that has a publish token set |
| `compression-enabled` | `true`           | Whether to compress the results when sending them to the server |
| `config-file`         | `projektor.json` | Path to a Node script config file |
| `print-link`          |                  | Set to `"true"` to set the `report-url` output from a report already published earlier in the job, instead of publishing again |

## Outputs

| Output       | Description |
|--------------|-------------|
| `report-url` | Link to the Projektor test report |

## Git information

When running in GitHub Actions, the action automatically sends the repository, branch, and commit SHA
with your results. That's what powers Projektor features like
[flaky test detection](/docs/flaky-test/), coverage trends, [pull request comments](/docs/github-pull-request/),
and [Slack notifications](/docs/slack-notification/) - no extra configuration needed.
