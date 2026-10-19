---
title: 'Server'
date: 2020-05-02T19:30:08+10:00
draft: false
weight: 18
group: 'Server & API'
summary: Configuring and running the Projektor server
---

## Requirements

* [Projektor server .jar file from releases page](https://github.com/craigatk/projektor/releases)
* Java 21+ for Projektor server v6.x.x
* Java 17+ for Projektor server v5.x.x
* Java 11+ for Projektor server v4.x.x
* Postgres (or, as an alpha feature in v6.6.0+, an S3-compatible object store - see [object store persistence](#object-store-persistence))
* (optional) S3-compatible object store for storing attachments

## Changelog

[Projektor server changelog](https://github.com/craigatk/projektor/blob/main/CHANGELOG.md#projektor-server)

## Startup

Set up the environment variables listed below to configure the features you want to use.
The bare minimum set of configuration to start Projektor is the database URL, username, and password. 

Then to start the server, simply run `java -jar projektor-server-<version>.jar`

### Port

The server listens on port `8080` by default. To use a different port, set:

```
PORT=<port for the server to listen on>
```

### Docker

The Projektor repository includes a `Dockerfile` that builds the server and UI from source into an image
that runs on Java 21:

```
git clone https://github.com/craigatk/projektor.git
cd projektor
docker build -t projektor .
```

Then run it with the same environment variables you'd use when running the `.jar` directly:

```
docker run -p 8080:8080 \
  -e DB_URL=jdbc:postgresql://<db-host>:5432/projektordb \
  -e DB_USERNAME=<username> \
  -e DB_PASSWORD=<password> \
  projektor
```

### InaccessibleObjectException troubleshooting

If you encounter `InaccessibleObjectException` exceptions (for example, when Projektor posts
[pull request comments](/docs/github-pull-request/)), add these params to the `java` execution:

`java --add-opens=java.base/sun.net.www.protocol.https=ALL-UNNAMED --add-opens=java.base/java.net=ALL-UNNAMED -jar projektor-server-<version>.jar`

The library that Projektor uses for posting comments to GitHub PRs contains reflection code that can be
blocked by the stricter module access checks in Java 17 and later, including the Java 21+ that
Projektor server v6 requires.

## Database configuration

Projektor stores the parsed test results in a Postgres database.
To configure which database Projektor connects to, please set the following environment variables:

```
DB_USERNAME=<username>
DB_PASSWORD=<password>
DB_URL=<url>
```

For example, to connect to a local database:

```
DB_USERNAME=testuser
DB_PASSWORD=testpass
DB_URL=jdbc:postgresql://localhost:5432/projektordb
```

`DATABASE_URL` is also accepted as an alternative to `DB_URL`, for hosts that provide connection
info as a `postgres://user:password@host:port/database` URI rather than a `jdbc:postgresql://`
URL — it's converted automatically. If both are set, `DB_URL` takes precedence.

You can also configure the maximum number of pooled database connections (default `10`):

```
DB_MAX_POOL_SIZE=<optional - maximum number of pooled database connections>
```

### Schema

You can optionally set the database schema to use by setting the `DB_SCHEMA` environment variable.
If that is not set, the server will default to the `public` schema.

## Object store persistence

**Alpha:** Starting in v6.6.0, Projektor can [store its data in an S3-compatible object store](/docs/object-store-persistence/)
instead of Postgres. When enabled, the database configuration above isn't needed. To enable it, set:

```
PERSISTENCE_TYPE=<database (default) or object-store>
PERSISTENCE_OBJECT_STORE_URL=<url of the object store>
PERSISTENCE_OBJECT_STORE_BUCKET_NAME=<bucket>
PERSISTENCE_OBJECT_STORE_ACCESS_KEY=<access_key>
PERSISTENCE_OBJECT_STORE_SECRET_KEY=<secret_key>
PERSISTENCE_OBJECT_STORE_KEY_PREFIX=<optional - prefix to put all keys under, so the data can share a bucket with other data>
PERSISTENCE_OBJECT_STORE_AUTO_CREATE_BUCKET=<optional - true to create the bucket on startup if it does not exist, defaults to false>
```

The object store must support conditional writes, see [supported object stores](/docs/object-store-persistence/#supported-object-stores).

## Authentication

By default the Projektor server accepts publishing requests (test results, code coverage,
attachments) from anyone who can reach it. To require a token on those requests, set:

```
PUBLISH_TOKEN=<a secret token of your choosing>
```

When set, publishing requests must send that same value in the `X-PROJEKTOR-TOKEN` header or they'll
be rejected — configure the Gradle plugin, Node script, or GitHub Action with the same token. If
`PUBLISH_TOKEN` isn't set, authentication is disabled and any request is accepted.

## Attachment object store configuration

If you want to support adding attachments (like Cypress screenshots) to your Projektor reports,
configure an S3-compatible object store by setting the following environment variables:

```
ATTACHMENT_URL=<url>
ATTACHMENT_BUCKET_NAME=<bucket>
ATTACHMENT_ACCESS_KEY=<access_key>
ATTACHMENT_SECRET_KEY=<secret_key>
```

There are also a couple more optional parameters you can specify:

```
ATTACHMENT_AUTO_CREATE_BUCKET=< true to automatically create the bucket on startup if it does not exist >
ATTACHMENT_MAX_SIZE_MB=< attachments above this max size in MB will be rejected >
```

## Payload size limit

The Projektor server rejects incoming test result payloads larger than a configurable size,
defaulting to 50,000,000 bytes (50MB):

```
MAX_PAYLOAD_SIZE=<optional - max size in bytes of an incoming test results payload, defaults to 50000000>
```

## Metrics

The Projektor server supports publishing metrics via [Micrometer](https://micrometer.io/docs/registry/influx) 
to [InfluxDB](https://docs.influxdata.com/influxdb/v1.8/). 
InfluxDB metrics are easy to graph and visualize using tools such as [Grafana](https://grafana.com/docs/grafana/latest/).

### Metrics configuration

Environment variables to set to configure the metrics:

```
METRICS_INFLUXDB_ENABLED=< true to enable metrics collection and publishing >
METRICS_INFLUXDB_URI=< URI of the InfluxDB server >
METRICS_INFLUXDB_DB_NAME=< name of the InfluxDB database to store metrics >
METRICS_INFLUXDB_AUTO_CREATE_DB=< whether to automatically create the InfluxDB database if it does not already exist >
METRICS_INFLUXDB_USERNAME=< optional - username for the InfluxDB server if it has auth enabled >
METRICS_INFLUXDB_PASSWORD=< optional - password for the InfluxDB server if it has auth enabled >
METRICS_INFLUXDB_INTERVAL=< optional - how often to publish metrics - defaults to every 10 seconds >
METRICS_INFLUXDB_ENV=< optional - environment tag to add to all metrics >
```

### Published metrics

The Projektor server is built with [ktor](https://ktor.io) and publishes the default
ktor server metrics as well as a few custom metrics:

* [ktor default metrics](https://ktor.io/servers/features/metrics-micrometer.html#exposed-metrics)
* `results_process_start` - when results processing starts
* `results_process_success` - when results sent to the server are successfully processed
* `results_parse_failure` - when there is an error parsing the results sent to the server
* `results_process_failure` - when there is an error processing results (any error besides a parse error)
* `coverage_process_start` - when processing a code coverage report starts
* `coverage_process_success` - when a code coverage report sent to the server is successfully processed
* `coverage_parse_failure` - when there is an error parsing a code coverage report sent to the server
* `coverage_process_failure` - when there is an error processing a code coverage report (any error besides a parse error)
* `pull_request_comment_success` - when the server successfully comments on a pull request
* `pull_request_comment_failure` - when there is a failure commenting a pull request
* `slack_notification_success` - when the server successfully posts a [Slack notification](/docs/slack-notification/)
* `slack_notification_failure` - when there is a failure posting a Slack notification

The list of metrics is also in the server codebase in `MetricsService.kt`

### Persistence metrics

Starting in v6.7.0, the server also publishes metrics on its storage, for both the Postgres and
[object store](/docs/object-store-persistence/) backends:

* `persistence_repository_call` - timer for each call to the storage layer, tagged with `backend`
  (`database` or `object-store`), `repository`, `method`, and `outcome` (`success`, `error`, or `cancelled`)
* `object_store_request` - timer for each request to an object store, tagged with `operation`
  (for example `get`, `put`, `list`, `delete`), `bucket`, and `outcome` (`success`, `not_found`,
  `precondition_failed`, or `error`). Covers attachment requests as well as object store persistence.
* `object_store_update_conflict` - counter, tagged with `bucket`, for each time an object store update lost a race
  with another writer and was retried
* `object_store_update_exhausted` - counter, tagged with `bucket`, for each object store update that gave up after
  too many conflicting writers

With the Postgres backend, the database connection pool also publishes the standard HikariCP
connection pool metrics (`hikaricp_connections_*`).

### Tracing

When the server runs with the [OpenTelemetry Java agent](https://opentelemetry.io/docs/zero-code/java/agent/),
each storage call gets a span named `projektor.<repository>.<method>` (for example
`projektor.TestRunRepository.fetchTestRun`), with the test run ID in `projektor.id` when the call has one.
The database queries or object store requests (`projektor.objectStore.<operation>`) the call makes are
nested under it.

### Slow storage calls

The server logs a warning for each storage call that takes longer than a threshold, defaulting to 1000 ms,
whether or not metrics are enabled:

```
PERSISTENCE_SLOW_CALL_THRESHOLD_MS=<optional - log storage calls slower than this many milliseconds, defaults to 1000>
```

## Database cleanup

To keep the database size manageable, Projektor has a scheduled job that runs once a day 
and cleans up test runs and attachments that are older than a specified number of days:

```
MAX_REPORT_AGE_DAYS=<test runs created more than X days will be deleted>
MAX_ATTACHMENT_AGE_DAYS=<attachments created more than X days ago will be deleted>
CLEANUP_DRY_RUN=<optional - set to true to just log the number of reports that would be cleaned up and not actually delete them>
```

## Messages

It can be helpful to display a message to all users in the Projektor UI if
you want to notify them of upcoming changes, etc. To do that, set this
environment variable:

```
GLOBAL_MESSAGES=<Messages to display to all users in the Projektor UI. If you want to show multiple messages, use a pipe | to separate them. > 
```

## GitHub links

Projektor can link directly to files in the GitHub UI, for example linking to uncovered or partial lines
on the code coverage page. To enable that linking capability, you'll need to set the
base URL for GitHub (either public GitHub or a GitHub enterprise instance):

```
GITHUB_BASE_URL=<base URL of the GitHub instance, for example https://github.com for public GitHub>
```

## GitHub pull request comments

Projektor can [comment on pull requests](../github-pull-request) with direct links to the Projektor
report, test pass/fail counts, code coverage stats, etc. To help configure that capability, set the following
environment variables:

```
SERVER_BASE_URL=<base URL of the Projektor server>
GITHUB_API_URL=<base URL of the GitHub API, either the public GitHub API or a GitHub Enterprise instance. For example, for public GitHub use https://api.github.com
GITHUB_APP_ID=<GitHub app ID for the Projektor GitHub app instance>
GITHUB_PRIVATE_KEY=<Base64-encoded private key for the Projektor GitHub app>
```

## Slack notifications

Projektor can [post Slack notifications](/docs/slack-notification/) (alpha) when a mainline build goes red,
drops code coverage, or makes a test flaky. To enable them, set the following environment variables:

```
SERVER_BASE_URL=<base URL of the Projektor server>
SLACK_API_URL=<base URL of the Slack Web API, https://slack.com/api>
SLACK_BOT_TOKEN=<bot token of the Projektor Slack app, xoxb-...>
SLACK_COVERAGE_DROP_THRESHOLD=<optional - minimum drop in line coverage percentage points to notify about, defaults to 0.1>
SLACK_FLAKY_MAX_RUNS=<optional - number of recent mainline builds to check for flaky tests, defaults to 50>
SLACK_FLAKY_FAILURE_THRESHOLD=<optional - number of failures in those builds for a test to be flaky, defaults to 5>
```

## AI test failure analysis

Projektor can [analyze test failures with OpenAI's ChatGPT or Anthropic's Claude](/docs/ai/). To enable it, set a
base64-encoded API key for at least one provider:

```
AI_PROVIDER=<optional - openai or anthropic. Defaults to whichever provider has an API key, preferring OpenAI>
OPENAI_API_KEY=<base64-encoded OpenAI API key>
ANTHROPIC_API_KEY=<base64-encoded Anthropic API key>
ANTHROPIC_MODEL=<optional - Claude model ID to use, defaults to claude-opus-5-5>
```

See [AI test failure analysis](/docs/ai/#configuration) for how the provider is chosen.

## Logging

By default the server logs plain text to the console. To switch to JSON (Logstash-formatted)
console logging instead, set:

```
JSON_LOGGER=<optional - any non-empty value enables JSON console logging>
```

## Troubleshooting

As with any software app, sometimes things go wrong when the Projektor server is
processing incoming test results and coverage reports. Sometimes these problems
are related to the incoming data (malformed test results .xml files, etc.),
issues reaching the database, etc.

To help diagnose what exactly happened when processing incoming results fails,
Projektor has an admin screen at `/admin` where you can view the most recent failures:

![Server failures](/images/server/projektor-admin-failures.png "Server failures")

By default the screen will show the last 10 failures, but you can specify
to load more failures as needed.

In addition to the failure message, sometimes it is helpful to look at the raw
incoming results body to help identify what went wrong. As these results bodies are
huge it's often more helpful to view them in an IDE where you can auto-format the payload
for easier viewing. To do that, you can click the link to copy the results body
to your clipboard then paste it into your favorite editor for easier formatting.
