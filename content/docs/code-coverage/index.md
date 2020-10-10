---
title: 'Code coverage'
date: 2020-10-10T19:30:08+10:00
draft: false
weight: 3
summary: Viewing code coverage stats and trends
---

Projektor can visualize code coverage stats in your codebase, helping identify
areas that need further testing.

Instructions for enabling code coverage with each publisher:

* [Gradle plugin](../gradle-plugin/#code-coverage)
* [Node script](../node-script/#code-coverage)

## Overall coverage stats

On the test run summary page, Projektor can display the overall coverage stats for your project:

![Overall coverage](/images/code-coverage/test-run-overall-coverage.png "Overall coverage")

## Per-project coverage

In Gradle multi-project builds, Projektor can break down the coverage stats for each sub-project:

![Coverage groups](/images/code-coverage/coverage-groups.png "Coverage-groups")

## Repository coverage trend

View the code coverage over time in a given Git repository:

![Repository coverage timeline](/images/code-coverage/repo-coverage-timeline.png "Repository coverage timeline")

## Organization coverage

View the code coverage stats for repos across your Git organization
to identify areas to focus on:

![Organization code coverage](/images/code-coverage/org-coverage.png "Organization code coverage")