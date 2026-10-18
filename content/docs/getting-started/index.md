---
title: 'Getting started'
date: 2020-05-07T19:30:08+10:00
draft: false
weight: 2
group: 'Getting started'
summary: Getting started with Projektor test reports
---

Getting your test results into Projektor takes three steps: run the server, make sure your tests write
JUnit XML results, and add a publisher to your build.

## 1. Run the Projektor server

The Projektor server is a single Java application backed by a Postgres database.

1. Download the latest `projektor-server-<version>.jar` from the
   [releases page](https://github.com/craigatk/projektor/releases). The server requires Java 21+.
2. Point it at your Postgres database and start it:

   ```
   DB_URL=jdbc:postgresql://localhost:5432/projektordb \
   DB_USERNAME=<username> \
   DB_PASSWORD=<password> \
   java -jar projektor-server-<version>.jar
   ```

3. Open `http://localhost:8080` to check that it's running.

See the [server page](/docs/server/) for all the configuration options, such as requiring a publish token
or storing attachments.

## 2. Write test results Projektor can read

Projektor reads test results in JUnit's XML format, which most test frameworks can write:

* **Gradle** `Test` tasks (JUnit, Spock, Kotest, etc.) write JUnit XML out of the box
* **Jest** needs the `jest-junit` reporter - see [Jest tests](/docs/jest/)
* **Cypress**, **Mocha**, **pytest**, **Go** (via [gotestsum](https://github.com/gotestyourself/gotestsum)),
  and many others can write JUnit XML with a reporter or command-line flag

## 3. Publish your results

Pick the publisher that fits your project:

| Publisher | Best for |
|-----------|----------|
| [Gradle plugin](/docs/gradle-plugin/) | Gradle projects - collects test results and Jacoco/Kover coverage automatically |
| [Node script](/docs/node-script/) | JavaScript projects, or any project that can run `npx` |
| [GitHub Action](/docs/github-action/) | Any project built with GitHub Actions, with no changes to the project itself |

For example, with the Gradle plugin all you need is:

```
plugins {
  id "dev.projektor.publish" version "11.0.1"
}

projektor {
  serverUrl = "https://projektor.example.com"
}
```

Now when your tests run in CI (or fail locally), the publisher sends the results to Projektor and logs
a link to the report.

## What's next

Once your results are in Projektor, you can add more context to your reports:

* [Code coverage](/docs/code-coverage/) - track coverage per build and over time
* [GitHub pull request comments](/docs/github-pull-request/) - link each pull request to its test report
* [Slack notifications](/docs/slack-notification/) - find out when the mainline build goes red
* [AI test failure analysis](/docs/ai/) - get suggestions on why a test failed
* [MCP server](/docs/mcp-server/) - let AI coding assistants query your test failures directly
