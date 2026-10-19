---
title: 'Object store persistence'
date: 2026-10-18T19:30:08+10:00
draft: false
weight: 18
group: 'Server & API'
summary: Run the Projektor server with an S3-compatible object store instead of Postgres
---

**Alpha:** Object store persistence is an alpha feature, added in Projektor server v6.6.0. The configuration
and the layout of the stored data may change in future releases, and there is no upgrade path for the
stored data yet. Postgres is still the default and recommended storage for production use.

By default the Projektor server stores test results, code coverage, and the rest of its data in
[Postgres](/docs/server/#database-configuration). Starting in v6.6.0 it can instead store everything in an
S3-compatible object store, so you can run Projektor without a database.

With object store persistence, the server doesn't connect to Postgres or run database migrations at all,
so you don't need to set the `DB_*` environment variables.

## Supported object stores

The object store must support conditional writes (the `If-Match` and `If-None-Match` headers), which
Projektor uses in place of database transactions. These stores support them:

* AWS S3
* MinIO
* RustFS

Some other S3-compatible stores (for example Google Cloud Storage's S3 interoperability) may not honor
conditional writes and aren't supported.

## Configuration

To use object store persistence, set `PERSISTENCE_TYPE` to `object-store` and point the server at a bucket:

```
PERSISTENCE_TYPE=object-store
PERSISTENCE_OBJECT_STORE_URL=<url of the object store>
PERSISTENCE_OBJECT_STORE_BUCKET_NAME=<bucket>
PERSISTENCE_OBJECT_STORE_ACCESS_KEY=<access_key>
PERSISTENCE_OBJECT_STORE_SECRET_KEY=<secret_key>
PERSISTENCE_OBJECT_STORE_KEY_PREFIX=<optional - prefix to put all keys under, so the data can share a bucket with other data>
PERSISTENCE_OBJECT_STORE_AUTO_CREATE_BUCKET=<optional - true to create the bucket on startup if it does not exist, defaults to false>
```

`PERSISTENCE_TYPE` defaults to `database` (Postgres). The URL, bucket name, access key, and secret key are
required when using `object-store`, and the server fails to start if any are missing. If the bucket doesn't
exist and `PERSISTENCE_OBJECT_STORE_AUTO_CREATE_BUCKET` isn't `true`, the server also fails to start.

For example, to run against a local MinIO server:

```
PERSISTENCE_TYPE=object-store
PERSISTENCE_OBJECT_STORE_URL=http://localhost:9000
PERSISTENCE_OBJECT_STORE_BUCKET_NAME=projektor
PERSISTENCE_OBJECT_STORE_ACCESS_KEY=minio_access_key
PERSISTENCE_OBJECT_STORE_SECRET_KEY=minio_secret_key
PERSISTENCE_OBJECT_STORE_AUTO_CREATE_BUCKET=true
```

### Attachments

[Attachments](/docs/server/#attachment-object-store-configuration) are still configured separately with
the `ATTACHMENT_*` environment variables. Object store persistence stores the list of each test run's
attachments, while the attachment files themselves go to the attachment bucket.

You can use the same bucket for both. If you do, set `PERSISTENCE_OBJECT_STORE_KEY_PREFIX` (for example
to `data/`) so Projektor's data is kept separate from the attachment files.

## Limitations

* **No migration from Postgres.** Switching `PERSISTENCE_TYPE` doesn't move existing data. A server
  switched from `database` to `object-store` starts with no test runs.
* **Repository pages slow down as history grows.** Repository-level pages and queries (timelines, coverage
  trends, flaky tests, test case history) read the repository's whole history from the object store.
  Configure [cleanup](/docs/server/#database-cleanup) with `MAX_REPORT_AGE_DAYS` to keep that history
  bounded.
* **No storage metrics before v6.7.0.** Starting in v6.7.0 the server publishes
  [persistence metrics](/docs/server/#persistence-metrics) and traces for object store reads and writes.
