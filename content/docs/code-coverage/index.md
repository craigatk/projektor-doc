---
title: 'Code coverage'
date: 2020-10-10T19:30:08+10:00
draft: false
weight: 3
group: 'Features'
summary: Viewing code coverage stats and trends
---

Projektor can visualize code coverage stats in your codebase, helping identify
areas that need further testing. 
Projektor supports code coverage reports in these formats:

| Format | Produced by |
|--------|-------------|
| [Jacoco](https://docs.gradle.org/current/userguide/jacoco_plugin.html) XML | Jacoco, and [Kover](/docs/gradle-plugin/#kover-code-coverage) using the Jacoco engine |
| Clover XML | [Jest](https://jestjs.io/docs/en/cli.html#--coverageboolean) (`clover.xml`) and other JavaScript coverage tools |
| [Cobertura](https://cobertura.github.io/cobertura/) XML | Cobertura and the many tools that can write Cobertura-format reports |
| [Go coverage profile](#go) | `go test -coverprofile` (Projektor server >=6.1.0) |

Projektor detects the format of each report automatically.
If your code coverage tool can output a coverage report in one of those formats, you're golden.

Example Projektor report with code coverage:

* [Jacoco code coverage](https://live.projektor.dev/tests/AYAM6TSB8DIS/coverage)
* [Jest code coverage](https://live.projektor.dev/tests/6M7RSLUA9UKC/coverage/All%20files/files)

Instructions for enabling code coverage with each publisher:

* [Gradle plugin](../gradle-plugin/#code-coverage)
* [Node script](../node-script/#code-coverage)
* [GitHub Action](../github-action/)

### Go

Projektor reads Go's native coverage profile format, the file written by `go test -coverprofile`:

`go test -coverprofile=coverage.out ./...`

Then publish the coverage profile along with your test results, for example with the Node script:

`npx projektor-publish --serverUrl=<projektor_url> --coverage=coverage.out test-results/*.xml`

Go's test runner doesn't write JUnit XML itself, so use a tool such as
[gotestsum](https://github.com/gotestyourself/gotestsum) (`gotestsum --junitfile test-results/results.xml`)
to write test results Projektor can read.

## Overall coverage stats

On the test run summary page, Projektor can display the overall coverage stats for your project:

![Overall coverage](/images/code-coverage/test-run-overall-coverage.png "Overall coverage")

## Per-project coverage

In Gradle multi-project builds, Projektor can break down the coverage stats for each sub-project:

![Coverage groups](/images/code-coverage/coverage-groups.png "Coverage-groups")

## Repository coverage trend

View the code coverage over time in a given Git repository:

![Repository coverage timeline](/images/code-coverage/repo-coverage-timeline.png "Repository coverage timeline")

[Repository code coverage timeline example](https://live.projektor.dev/repository/craigatk/projektor/coverage)

## Organization coverage

View the code coverage stats for repos across your Git organization
to identify areas to focus on:

![Organization code coverage](/images/code-coverage/org-coverage.png "Organization code coverage")

[Organization code coverage example](https://live.projektor.dev/organization/craigatk/coverage)
