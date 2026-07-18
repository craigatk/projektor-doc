---
title: 'Architecture'
date: 2026-07-18T19:30:08+10:00
draft: false
weight: 13
summary: How Projektor's components fit together
---

Projektor is made up of a few simple pieces that turn your CI test output into a shareable
dashboard:

* **Test run** — your project's test suite runs in CI (or locally) and produces JUnit XML, Jest,
  or other supported test output.
* **Projektor plugin** — the [Gradle plugin](/docs/gradle-plugin/), [Node script](/docs/node-script/),
  or GitHub Action collects those results as part of the test run and sends them to the Projektor
  server.
* **Projektor server** — parses the incoming results, stores structured test, coverage, and
  performance data in [Postgres](/docs/server/), and stores larger binary attachments like
  screenshots in an S3-compatible object store.
* **Projektor UI** — queries the server to render test results, attachments, coverage, and trends
  in the browser.

Because parsing and storage happen entirely on the server, your CI environment only ever needs
the lightweight publishing plugin — it never talks to Postgres or the object store directly.

![Projektor architecture](/images/architecture/Projektor-architecture.png "Projektor architecture")
