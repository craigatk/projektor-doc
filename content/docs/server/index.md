---
title: 'Server'
date: 2020-05-02T19:30:08+10:00
draft: false
weight: 4
summary: Configuring and running the Projektor server
---

## Requirements

* Java 11
* Postgres
* (optional) S3-compatible object store for storing attachments

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

### Schema

You can optionally set the database schema to use by setting the `DB_SCHEMA` environment variable.
If that is not set, the server will default to the `public` schema.

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
