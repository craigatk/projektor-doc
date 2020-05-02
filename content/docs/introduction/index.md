---
title: 'Introduction'
date: 2020-05-02T19:30:08+10:00
draft: false
weight: 1
summary: How Projektor can make debugging test failures in CI easier and faster
---

Tests failing on your machine and need help debugging them? Or tests are passing local but failing in CI and
CI doesn't record the test report?
Access and share your test reports quickly and easily with Projektor.

## How it works

Many testing frameworks support writing their results in JUnit's XML format.
To support showing results from the most types of testing frameworks, Projektor parses these
JUnit XML results, saves them in a Postgres database, then makes them available to you and your
team with a simple, shareable URL.

Simply send your JUnit XML results from whichever testing framework you use to the `/results`
endpoint in your Projektor server. Or use one of the convenient publishing plugins to
automatically publish your test results as part of your test executions.
Then you will get back the URL to view your results.

## Examples

Projektor shows a summary of all the tests executed as part of your test run:

https://projektorlive.herokuapp.com/tests/42ZQNMQBEBCD

Showing things like number of tests executed, how many passed or failed, etc.
And if there are any failures, those failure details are shown first on the dashboard:

https://projektorlive.herokuapp.com/tests/RA1FTOGJBNKD

To help debug failures in any environment (especially CI), Projektor gives you access
to the system out and system err from each test:

https://projektorlive.herokuapp.com/tests/42ZQNMQBEBCD/suite/19/systemOut

To help you make your test suite faster, Projektor also shows the slowest 10 test cases to find
which tests to focus on to speed up your overall test run:

https://projektorlive.herokuapp.com/tests/42ZQNMQBEBCD/slow