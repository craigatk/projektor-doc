---
title: 'Code coverage'
date: 2020-10-10T19:30:08+10:00
draft: false
weight: 3
summary: Viewing code coverage stats and trends
---

Projektor can visualize code coverage stats in your codebase, helping identify
areas that need further testing. 
Currently Projektor supports code coverage from 
[Jacoco](https://docs.gradle.org/current/userguide/jacoco_plugin.html) and 
[Jest](https://jestjs.io/docs/en/cli.html#--coverageboolean).

Example Projektor reports with code coverage:

* [Jacoco code coverage](https://projektorlive.herokuapp.com/tests/DELWE3XYEXJK/coverage)
* [Jest code coverage](https://projektorlive.herokuapp.com/tests/5NSUCYQV4MWS/coverage)

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

[Repository code coverage timeline example](https://projektorlive.herokuapp.com/repository/craigatk/projektor/coverage)

## Organization coverage

View the code coverage stats for repos across your Git organization
to identify areas to focus on:

![Organization code coverage](/images/code-coverage/org-coverage.png "Organization code coverage")

[Organization code coverage example](https://projektorlive.herokuapp.com/organization/craigatk/coverage)
