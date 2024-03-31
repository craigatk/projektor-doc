---
title: 'API'
date: 2023-03-29T19:30:08+10:00
draft: false
weight: 10
summary: Programmatically access Projektor data via a REST API
---

Projektor supports programmatic access to certain data elements via a REST API.

**NOTE: Projektor's API is available in Projektor server versions >=4.35.0**

## Headers

When accessing the Projektor API, always pass an `Accept: application/json` header 
(if your REST client doesn't already add one by default).

## Coverage

API endpoints available to retrieve coverage data from Projektor.

### Repository coverage

API endpoint to get the code coverage percentage for a given repository:

`GET /api/v1/repo/{org}/{repo}/coverage/current`

Example URL: https://projektorlive.herokuapp.com/api/v1/repo/craigatk/projektor/coverage/current

Example response:

```
{
    "id": "MGPYXGQ5EITI",
    "created_timestamp": "2024-03-30T14:59:04.918998Z",
    "covered_percentage": 96.37,
    "repo": "craigatk/projektor",
    "project": null,
    "branch": "main"
}
```

Response fields:

| Field name            | Description                                                   |
|-----------------------|---------------------------------------------------------------|
| `id`                  | Test run ID that produced the given code coverage data        |
| `created_timestamp`   | When the code coverage data was recorded                      |
| `covered_perecentage` | The percentage of covered lines in the repo                   |
| `repo`                | The name of the repo                                          |
| `project`             | The subproject in the repo (optional)                         |
| `branch`              | The name of the branch where the code coverage data came from |

### Organization coverage

API endpoint to get the code coverage percentage for all repos in a Git organization that have coverage data:

`GET /api/v1/org/{org}/coverage/current`

Example URL: https://projektorlive.herokuapp.com/api/v1/org/craigatk/coverage/current

Example response:

```
{
    "repositories": [
        {
            "id": "81YHD8KLKONL",
            "created_timestamp": "2023-04-25T10:52:51.196021Z",
            "covered_percentage": 96.37,
            "repo": "craigatk/projektor",
            "project": null,
            "branch": "main"
        },
        {
            "id": "ASDVRYW01Y7J",
            "created_timestamp": "2023-04-25T10:48:01.413402Z",
            "covered_percentage": 98.93,
            "repo": "craigatk/projektor",
            "project": "node-script",
            "branch": "main"
        },
        {
            "id": "G6EEG6H1PP9I",
            "created_timestamp": "2023-06-27T11:22:50.070180Z",
            "covered_percentage": 91.66,
            "repo": "craigatk/projektor",
            "project": "ui-jest",
            "branch": "main"
        }
    ]
}
```

Response fields:

| Field name            | Description                                                   |
|-----------------------|---------------------------------------------------------------|
| `id`                  | Test run ID that produced the given code coverage data        |
| `created_timestamp`   | When the code coverage data was recorded                      |
| `covered_perecentage` | The percentage of covered lines in the repo                   |
| `repo`                | The name of the repo                                          |
| `project`             | The subproject in the repo (optional)                         |
| `branch`              | The name of the branch where the code coverage data came from |

## Changelog

* Projektor server 4.35.0
  * Initial release of `GET /api/v1/repo/{org}/{repo}/coverage/current` and `GET /api/v1/org/{org}/coverage/current` endpoints