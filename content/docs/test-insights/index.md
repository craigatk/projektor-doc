---
title: 'Test failure insights'
date: 2026-10-18T19:30:08+10:00
draft: false
weight: 6
group: 'Features'
summary: Group failures by root cause, catch tests that got slower, and see a test's pass/fail history
---

Beyond showing what failed, Projektor looks across your test runs to help answer the follow-up questions:
are these failures all the same problem, did this test just get slower, and when did this test start failing?

## Failure clustering

When a lot of tests fail at once, it's often from a single root cause - a database that didn't start,
a shared fixture that broke, etc. Instead of making you click through every failure to figure that out,
Projektor groups a test run's failures by their root cause and shows a summary above the failed tests,
such as "3 tests failed from 1 root cause: SQLException in ResultsProcessingDatabaseRepository".

[Failure clustering example](https://live.projektor.dev/tests/RMFRFC19KCZF/failed)

Projektor groups failures by a normalized failure signature made up of:

* the root cause exception type
* the failure message, with values that change from run to run (IDs, numbers, etc.) masked out
* the first stack frame from your project's code

Each group shows the normalized failure message, and you can expand it to see all the tests in the group.
The summary is only shown when at least two failures share a root cause.

If [AI test failure analysis](/docs/ai/) is enabled, each group also has an "Analyze Root Cause" button
that sends one representative failure from the group for analysis. The analysis is cached for that
test run and root cause, so analyzing any other test in the same group reuses the same result
instead of making another call to the AI provider.

Failure clustering is available in Projektor server versions >=6.4.0.

## Slow test regressions

A test that suddenly takes much longer than it used to can be an early sign of a performance problem,
and slow tests add up to slow builds.
On the slow tests page, Projektor shows a "Slower than baseline" section listing tests that
took more than 50% longer than their baseline in this test run.

[Slow test regression example](https://live.projektor.dev/tests/DFM7BG7O5VHB/slow)

A test's baseline is the median duration of its passing runs in the previous 10 CI runs of the same
repository, project, and branch.
To avoid noise, a test is only flagged when:

* it has at least 3 passing baseline runs
* it's at least 0.1 seconds slower than its baseline

Slow test regressions require test runs published from CI with Git repository information, and are
available in Projektor server versions >=6.4.0.

## Test case history

Each test case has a "History" tab that shows its pass/fail results and duration across recent CI runs
of the same repository, project, and branch.

[Test case history example](https://live.projektor.dev/tests/DFM7BG7O5VHB/suite/21/case/1/history)

For a failing test, the history tab calls out where the current streak of failures started and the last
passing run before it, so you can quickly tell whether the failure is new or long-standing, and narrow
down which commit broke it. It also includes a chart of the test's duration over time and a table of
each run, linking to that run's report.

Test case history requires test runs published from CI with Git repository information, and is
available in Projektor server versions >=6.3.0.

AI coding assistants can get the same history from Projektor's
[MCP server](/docs/mcp-server/#get_test_case_history).
