---
title: 'Introduction'
date: 2020-05-02T19:30:08+10:00
draft: false
weight: 1
mermaid: true
summary: How Projektor can make debugging test failures in CI faster and easier
---

Tests failing on your machine and need help debugging them? Or tests are passing local but failing in CI and
CI doesn't record the test report? Debugging tests in these scenarios can be time consuming and painful,
especially if you don't have the full context of the test failure.

Access and share your full test reports quickly and easily with Projektor.

## How it works

Many testing frameworks support writing their results in JUnit's XML format.
To support showing results from the most types of testing frameworks, Projektor parses these
JUnit XML results, saves them in a Postgres database, then makes them available to you and your
team with a simple, shareable URL.

Simply use one of the convenient Projektor publishing plugins for [Gradle](/docs/gradle-plugin/)
or [Javascript/Node projects](/docs/node-script/) to
publish your test results as part of your test executions. These plugins handle collecting all
your project's test results and sending them to the Projektor server for processing and storage.
Then you will get back the URL to view your results in the Projektor UI.

## Examples

Projektor shows a summary of all the tests executed as part of your test run:

https://projektorlive.herokuapp.com/tests/RQ99P3LRZJSX/

The summary includes things like number of tests executed, how many passed or failed, etc.
And if there are any failures, those failure details are shown first on the dashboard:

https://projektorlive.herokuapp.com/tests/WF8TKEK86SWS

To help debug failures in any environment (especially CI), Projektor gives you access
to the system out and system err from each test:

https://projektorlive.herokuapp.com/tests/V1BMYK93MTNR/suite/65/systemOut

To help you make your test suite faster, Projektor also shows the slowest 10 test cases to find
which tests to focus on to speed up your overall test run:

https://projektorlive.herokuapp.com/tests/RQ99P3LRZJSX/slow

## Architecture

![Projektor architecture](/images/introduction/Projektor-architecture.png "Projektor architecture")
