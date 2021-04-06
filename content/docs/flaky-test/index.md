---
title: 'Flaky test detection'
date: 2020-10-04T19:30:08+10:00
draft: false
weight: 5
summary: Flaky test detection with Projektor
---

Flaky tests are a pain and slow down your development speed.
When you don't trust whether a test failure caught a legitimate issue or
if it was just the test intermittently failing, you lose confidence in your test suite.
And you waste time investigating whether the failure was real or not, and it takes
extra time to investigate if the intermittent failure is mainly in CI builds.

Projektor detects flaky tests and gives you a list of all the flaky tests in your repository
so you know which tests are the problem and can focus on stabilizing them to improve
your test suite and save your sanity.

![Flaky tests](/images/flaky-tests.png "Flaky tests")

By default Projektor considers a test flaky if it fails at least 5 times out of the last 50 builds,
but you can change those values as needed for your project.

Often test runs for pull request branches have more test failures due to the
code/tests being refined in those branches, so sometimes those failures on PR branches
can make it harder to find which tests are flaky.
To help with that, Projektor has the ability
to filter down to just failures on the mainline (`main` or `master` branch) when
calculating which tests are flaky.

To get to the flaky tests page, click on the "Repository" link on the left side nav in a test report.

## Prerequisites

Flaky test detection in Projektor requires [Gradle plugin v6.0.0+](/docs/gradle-plugin), 
[Node plugin v2.8.0+](/docs/node-script), or [GitHub Action v11+](https://github.com/craigatk/projektor-action).
