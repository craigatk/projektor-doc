---
title: 'MCP server'
date: 2026-07-18T19:30:08+10:00
draft: false
weight: 11
group: 'Features'
summary: Query Projektor test failure data from AI coding assistants via its MCP server
---

Projektor runs a [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server, so AI coding
assistants like Claude Code can pull test failure diagnostics directly from Projektor while working in
your codebase — for example, asking "why is this pull request's test failing?" and getting back the
actual failure message and stack trace instead of having to go find it manually.

## Endpoint

The MCP server is served from your Projektor server at:

`/mcp`

For example: `https://live.projektor.dev/mcp`

It speaks the MCP [Streamable HTTP transport](https://modelcontextprotocol.io/docs/concepts/transports)
and doesn't require any authentication, the same trust model as the rest of Projektor's REST API.

## Connecting a client

With the Claude Code CLI:

```
claude mcp add --transport http projektor https://live.projektor.dev/mcp
```

(substitute your own Projektor server's URL). Other MCP clients that support a remote HTTP server can
be pointed at the same URL, typically by adding an entry like this to their MCP server config:

```json
{
  "mcpServers": {
    "projektor": {
      "url": "https://live.projektor.dev/mcp"
    }
  }
}
```

## Tools

The MCP server is available in Projektor server versions >=6.4.0 and provides these tools:

| Tool | What it's for |
|------|---------------|
| [`get_pull_request_failing_test_context`](#get_pull_request_failing_test_context) | Why are this pull request's tests failing? |
| [`get_flaky_tests`](#get_flaky_tests) | Which tests in this repo are flaky? Is this failure a known flake? |
| [`get_test_case_history`](#get_test_case_history) | When did this test start failing, and which commit broke it? |
| [`get_coverage_delta`](#get_coverage_delta) | Where did this pull request lose code coverage? |
| [`get_uncovered_lines`](#get_uncovered_lines) | Which lines in this file still need tests? |

The tools are designed to chain together. For example, the `test_run_public_id`, `test_suite_idx`, and
`test_case_idx` returned by `get_pull_request_failing_test_context` or `get_flaky_tests` can be passed
straight to `get_test_case_history`.

When a tool doesn't find any data, such as no test run for the given pull request, it returns a message
saying so instead of an error.

### get_pull_request_failing_test_context

Fetches the diagnostic context (failure message, stack trace, stdout/stderr) for every currently-failing
test in the most recent Projektor test run recorded for a GitHub pull request, so it can be used to
diagnose why those tests are failing.

#### Parameters

| Parameter           | Description |
|---------------------|-------------|
| `pullRequestUrl`    | The GitHub pull request's URL, e.g. `https://github.com/{org}/{repo}/pull/{number}`. An alternative to passing `orgName`, `repoName`, and `pullRequestNumber` separately. |
| `orgName`           | GitHub organization or user name that owns the repository |
| `repoName`          | GitHub repository name |
| `pullRequestNumber` | The pull request number |

Identify the pull request either with `pullRequestUrl`, or with `orgName`, `repoName`, and
`pullRequestNumber` together.

#### Example question

With the MCP server connected, you can ask your assistant in plain language, e.g.:

> Why is this pull request's test failing: https://github.com/craigatk/projektor/pull/2543

The assistant will call `get_pull_request_failing_test_context` with that URL, get back the failing
test's name, failure message, and stack trace, and use that to explain the failure instead of asking you
to go dig it out of a CI log.

#### Example response

```json
{
  "test_run_public_id": "THEOQRSBH8DB",
  "failing_test_cases": [
    {
      "test_name": "should list code quality reports on code quality page.code quality should list code quality reports on code quality page",
      "test_suite_idx": 6,
      "test_case_idx": 1,
      "debug_context_markdown": "# Test Failure: code quality should list code quality reports on code quality page\n\n- **Full name:** should list code quality reports on code quality page.code quality should list code quality reports on code quality page\n- **Suite:** code quality\n- **Class:** should list code quality reports on code quality page\n- **File:** cypress/e2e/code_quality.cy.js\n- **Duration:** 0.000 ms\n\n## Failure\n\n**Type:** AssertionError\n\n**Message:**\n```\nTimed out retrying after 10000ms: expected '<span>' to contain 'github-line-67'\n```\n\n**Stack trace:**\n```\nAssertionError: Timed out retrying after 10000ms: expected '<span>' to contain 'github-line-67'\n    at Context.eval (webpack://projektor/./cypress/e2e/code_quality.cy.js:36:48)\n```"
    }
  ]
}
```

### get_flaky_tests

Lists the [flaky tests](/docs/flaky-test/) in a GitHub repository - tests that failed at least
`failureThreshold` times in the repository's most recent `maxRuns` test runs - most frequently failing first.
Each flaky test includes its first and latest failure, with the latest failure's message and stack trace
(truncated to 30 lines). Useful for finding tests worth stabilizing, or for checking whether a failing test
is a known flake before spending time debugging it.

#### Parameters

| Parameter          | Description |
|--------------------|-------------|
| `orgName`          | (Required) GitHub organization or user name that owns the repository |
| `repoName`         | (Required) GitHub repository name |
| `projectName`      | Project name, for repositories that publish results for multiple projects |
| `maxRuns`          | How many of the repository's most recent test runs to look at. Defaults to `50`, max `500` |
| `failureThreshold` | Minimum number of failures within those runs for a test to count as flaky. Defaults to `5` |
| `branchType`       | `ALL` (default) to count failures on every branch including pull requests, or `MAINLINE` for only the mainline branch |

Note that `branchType` defaults to `ALL` here, unlike the flaky tests page and
[REST API](/docs/api/#repository-flaky-tests), which default to the mainline branch.

#### Example question

> Are there any flaky tests in craigatk/projektor?

#### Example response

```json
{
  "repo": "craigatk/projektor",
  "project": null,
  "max_runs": 200,
  "failure_count_threshold": 2,
  "branch_type": "ALL",
  "tests": [
    {
      "test_name": "projektor.incomingresults.SaveResultsErrorApplicationTest.should still process results after multiple failures()",
      "class_name": "SaveResultsErrorApplicationTest",
      "file_name": null,
      "failure_count": 2,
      "failure_percentage": 1.00,
      "first_failure": {
        "test_run_public_id": "RMFRFC19KCZF",
        "test_suite_idx": 21,
        "test_case_idx": 1,
        "created_timestamp": "2026-09-28T14:00:02.473266",
        "failure_message": "org.jooq.exception.DataAccessException: Error getting connection from data source HikariDataSource (HikariPool-46)",
        "failure_text": null
      },
      "latest_failure": {
        "test_run_public_id": "DFM7BG7O5VHB",
        "test_suite_idx": 21,
        "test_case_idx": 1,
        "created_timestamp": "2026-10-01T21:49:24.63659",
        "failure_message": "org.jooq.exception.DataAccessException: Error getting connection from data source HikariDataSource (HikariPool-46)",
        "failure_text": "org.jooq.exception.DataAccessException: Error getting connection from data source HikariDataSource (HikariPool-46)\n\tat org.jooq_3.21.9.POSTGRES.debug(Unknown Source)\n\tat org.jooq.impl.DataSourceConnectionProvider.acquire(DataSourceConnectionProvider.java:90)\n..."
      }
    }
  ]
}
```

(Response from a call with `maxRuns` of `200` and `failureThreshold` of `2`, with `failure_text` shortened.)

### get_test_case_history

Fetches a test case's pass/fail history across the repository's recent test runs, most recent first,
starting from the given test run. It also reports `first_failure` (where the current streak of failures
began) and `last_passed_before_failure`, including their branch, commit SHA, and pull request number.
That helps tell a new regression apart from a long-standing or flaky failure, and narrow down which change
broke the test.

This is the same data shown in the [test case history tab](/docs/test-insights/#test-case-history) in the UI.

#### Parameters

| Parameter         | Description |
|-------------------|-------------|
| `testRunPublicId` | (Required) Public ID of a Projektor test run containing the test case |
| `testSuiteIdx`    | (Required) Index of the test suite within that test run |
| `testCaseIdx`     | (Required) Index of the test case within that test suite |
| `maxRuns`         | Maximum number of test runs to include. Defaults to `30`, max `100` |

The test case is identified by the `test_run_public_id`, `test_suite_idx`, and `test_case_idx` values
returned by `get_pull_request_failing_test_context` or `get_flaky_tests`.

#### Example question

> When did this test start failing, and what was the last commit where it passed?

#### Example response

```json
{
  "entries": [
    {
      "public_id": "DFM7BG7O5VHB",
      "test_suite_idx": 21,
      "test_case_idx": 1,
      "created_timestamp": "2026-10-01T21:49:24.63659",
      "passed": false,
      "skipped": false,
      "duration": 4.304,
      "branch_name": "main",
      "commit_sha": "876fa3294baa2ab4272e87a1385f03c4cad67c4b",
      "pull_request_number": null,
      "failed": true
    },
    {
      "public_id": "MTTRNTJ5NOTI",
      "test_suite_idx": 21,
      "test_case_idx": 1,
      "created_timestamp": "2026-09-30T10:50:53.77138",
      "passed": true,
      "skipped": false,
      "duration": 4.654,
      "branch_name": "main",
      "commit_sha": "316395a65210035d4585c0d27bda42822ac15b57",
      "pull_request_number": null,
      "failed": false
    }
  ],
  "first_failure": {
    "public_id": "DFM7BG7O5VHB",
    "test_suite_idx": 21,
    "test_case_idx": 1,
    "created_timestamp": "2026-10-01T21:49:24.63659",
    "passed": false,
    "skipped": false,
    "duration": 4.304,
    "branch_name": "main",
    "commit_sha": "876fa3294baa2ab4272e87a1385f03c4cad67c4b",
    "pull_request_number": null,
    "failed": true
  },
  "last_passed_before_failure": {
    "public_id": "MTTRNTJ5NOTI",
    "test_suite_idx": 21,
    "test_case_idx": 1,
    "created_timestamp": "2026-09-30T10:50:53.77138",
    "passed": true,
    "skipped": false,
    "duration": 4.654,
    "branch_name": "main",
    "commit_sha": "316395a65210035d4585c0d27bda42822ac15b57",
    "pull_request_number": null,
    "failed": false
  }
}
```

(Response from a call with `maxRuns` of `3`.)

History is found by matching other test runs from the same Git repository, so the test run needs to have
been published with Git repository information.

### get_coverage_delta

Compares the [code coverage](/docs/code-coverage/) of a GitHub pull request's most recent test run that
has coverage against the most recent mainline branch run before it. Returns the overall and per-module
(group) line, statement, and branch coverage with a `covered_percentage_delta`, plus the files whose
line coverage changed or that are new since the baseline - biggest coverage drops first - with their
missed and partially-covered line numbers.

#### Parameters

| Parameter           | Description |
|---------------------|-------------|
| `pullRequestUrl`    | The GitHub pull request's URL, e.g. `https://github.com/{org}/{repo}/pull/{number}`. An alternative to passing `orgName`, `repoName`, and `pullRequestNumber` separately. |
| `orgName`           | GitHub organization or user name that owns the repository |
| `repoName`          | GitHub repository name |
| `pullRequestNumber` | The pull request number |
| `maxChangedFiles`   | Maximum number of changed files to include. Defaults to `50`, max `500` |

Identify the pull request either with `pullRequestUrl`, or with `orgName`, `repoName`, and
`pullRequestNumber` together.

#### Example question

> Which files lost coverage in https://github.com/craigatk/projektor/pull/2790, and which lines need tests?

#### Response fields

| Field name                    | Description |
|-------------------------------|-------------|
| `test_run_public_id`          | The pull request's test run the coverage came from |
| `baseline_test_run_public_id` | The mainline test run it's compared against |
| `overall_stats`               | Overall `statement_stat`, `line_stat`, and `branch_stat`, each with `covered`, `missed`, `total`, `covered_percentage`, and `covered_percentage_delta` |
| `groups`                      | The same stats for each coverage group (e.g. each Gradle sub-project) |
| `changed_files`               | Files whose line coverage changed or that are new since the baseline, each with `group_name`, `file_path`, `line_covered_percentage`, `line_covered_percentage_delta` (`null` for new files), `missed_lines`, and `partial_lines` |
| `changed_files_truncated`     | Whether there were more changed files than `maxChangedFiles` |

#### Example response

```json
{
  "test_run_public_id": "UKAINGADQY2J",
  "baseline_test_run_public_id": "ODAXOD7D2MMC",
  "overall_stats": {
    "statement_stat": {
      "covered": 31873,
      "missed": 2081,
      "covered_percentage_delta": 0.0,
      "total": 33954,
      "covered_percentage": 93.87
    },
    "line_stat": {
      "covered": 4640,
      "missed": 260,
      "covered_percentage_delta": 0.0,
      "total": 4900,
      "covered_percentage": 94.69
    },
    "branch_stat": {
      "covered": 1150,
      "missed": 297,
      "covered_percentage_delta": 0.0,
      "total": 1447,
      "covered_percentage": 79.47
    }
  },
  "groups": [
    {
      "name": "server-app",
      "stats": {
        "statement_stat": { "covered": 27521, "missed": 1716, "covered_percentage_delta": 0.0, "total": 29237, "covered_percentage": 94.13 },
        "line_stat": { "covered": 3894, "missed": 210, "covered_percentage_delta": 0.0, "total": 4104, "covered_percentage": 94.88 },
        "branch_stat": { "covered": 929, "missed": 247, "covered_percentage_delta": 0.0, "total": 1176, "covered_percentage": 79.0 }
      }
    }
  ],
  "changed_files": [],
  "changed_files_truncated": false
}
```

(Response shortened to a single group.)

### get_uncovered_lines

Fetches the line numbers in a source file that tests don't cover (`missed_lines`) or only partially cover,
such as not every branch taken (`partial_lines`), along with the file's line and branch coverage.

#### Parameters

| Parameter           | Description |
|---------------------|-------------|
| `filePath`          | (Required) Path of the source file. Can be a repository-relative path (`src/main/kotlin/com/example/Foo.kt`), a package-relative path (`com/example/Foo.kt`), or just the file name (`Foo.kt`). A file name that matches more than one file returns them all. |
| `testRunPublicId`   | Public ID of a specific Projektor test run to read coverage from |
| `pullRequestUrl`    | Read coverage from the most recent test run with coverage for this GitHub pull request |
| `orgName`           | GitHub organization or user name that owns the repository |
| `repoName`          | GitHub repository name |
| `pullRequestNumber` | The pull request number, used with `orgName` and `repoName` |
| `projectName`       | Project name, for repositories that publish results for multiple projects (mainline lookup only) |

The coverage is read from, in order of precedence:

1. `testRunPublicId`, if given
2. The pull request's most recent test run with coverage, if `pullRequestUrl` or `pullRequestNumber` is given
3. The most recent mainline branch test run with coverage in `orgName`/`repoName`

#### Example question

> Which lines in FlakyTestsTool.kt aren't fully covered on main in craigatk/projektor?

#### Example response

```json
{
  "test_run_public_id": "BFIEUOILBYPQ",
  "files": [
    {
      "group_name": "server-app",
      "file_path": "server/server-app/src/main/kotlin/projektor/mcp/FlakyTestsTool.kt",
      "line_stat": {
        "covered": 87,
        "missed": 0,
        "covered_percentage_delta": null,
        "total": 87,
        "covered_percentage": 100.0
      },
      "branch_stat": {
        "covered": 21,
        "missed": 11,
        "covered_percentage_delta": null,
        "total": 32,
        "covered_percentage": 65.62
      },
      "missed_lines": [],
      "partial_lines": [54, 57, 102, 105, 106, 121]
    }
  ]
}
```

## Changelog

* Projektor server 6.4.0
  * Initial release of the MCP server with the `get_pull_request_failing_test_context`, `get_flaky_tests`,
    `get_test_case_history`, `get_coverage_delta`, and `get_uncovered_lines` tools
