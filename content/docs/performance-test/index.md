---
title: 'Performance tests'
date: 2020-11-15T19:30:08+10:00
draft: false
weight: 4
summary: View performance test results with Projektor, both individual test runs and performance graphed over time
---

Performance / load tests are a great way to measure how much load your application can handle.
And running these tests on a regular basis can help guard against performance regressions,
as well as illustrate the impact of performance improvements.

Projektor supports showing key performance stats (transactions per second, p95 response time, etc.)
for individual test runs and over time.

## Performance results

### Individual performance test run

You can view the performance results stats for a [specific performance test run](https://projektorlive.herokuapp.com/tests/F7JKK2XJSQ2P).

![Performance results individual](/images/performance-test/projektor-performance-single.png "Performance results individual")

### Performance results over time

And you can see [performance over time](https://projektorlive.herokuapp.com/repository/craigatk/projektor/performance) for each performance test run in a Git repository.

![Performance results graph](/images/performance-test/projektor-performance-graph.png "Performance results graph")

## Configuration 

Currently Projektor supports results from [k6 performance tests](https://k6.io/).

### k6

You can view k6 results in Projektor with only a small tweak to your k6 executions. 
When running your k6 tests, configure k6 to [export the results summary to JSON](https://k6.io/docs/getting-started/results-output#summary-export)

`k6 run --summary-export=perf-test.json load-test-script.js`

### Projektor

Use the `performance` config parameter to send performance results to the Projektor server. 
Please see the [performance section of the Node script docs](../node-script/#performance-test-results) 
for more details.
