---
title: 'API'
date: 2023-03-29T19:30:08+10:00
draft: false
weight: 10
summary: Programmatically access Projektor data via a REST API
---

Projektor supports programmatic access to certain data elements via a REST API.

## Headers

When accessing the Projektor API, always pass an `Accept: application/json` header 
(if your REST client doesn't already add one by default).

## Coverage

API endpoints available to retrieve coverage data from Projektor.

### Repository coverage

API endpoint to get the code coverage percentage for a given repository:

`GET /api/v1/repo/{org}/{repo}/coverage/current`

Example URL: https://projektorlive.herokuapp.com/api/v1/repo/craigatk/projektor/coverage/current

Available in Projektor server versions >=4.35.0

#### Response fields

| Field name            | Description                                                   |
|-----------------------|---------------------------------------------------------------|
| `id`                  | Test run ID that produced the given code coverage data        |
| `created_timestamp`   | When the code coverage data was recorded                      |
| `covered_perecentage` | The percentage of covered lines in the repo                   |
| `repo`                | The name of the repo                                          |
| `project`             | The subproject in the repo (optional)                         |
| `branch`              | The name of the branch where the code coverage data came from |

#### Example response

```json
{
    "id": "MGPYXGQ5EITI",
    "created_timestamp": "2024-03-30T14:59:04.918998Z",
    "covered_percentage": 96.37,
    "repo": "craigatk/projektor",
    "project": null,
    "branch": "main"
}
```

### Organization coverage

API endpoint to get the code coverage percentage for all repos in a Git organization that have coverage data:

`GET /api/v1/org/{org}/coverage/current`

Example URL: https://projektorlive.herokuapp.com/api/v1/org/craigatk/coverage/current

Available in Projektor server versions >=4.35.0

#### Response fields

| Field name            | Description                                                   |
|-----------------------|---------------------------------------------------------------|
| `id`                  | Test run ID that produced the given code coverage data        |
| `created_timestamp`   | When the code coverage data was recorded                      |
| `covered_perecentage` | The percentage of covered lines in the repo                   |
| `repo`                | The name of the repo                                          |
| `project`             | The subproject in the repo (optional)                         |
| `branch`              | The name of the branch where the code coverage data came from |

#### Example response

```json
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

## Flaky tests

### Repository flaky tests

API endpoint to find the flaky tests in a repository:

`GET /api/v1/repo/{org}/{repo}/tests/flaky`

Available in Projektor server versions >=4.38.0

#### Request query options

| Query parameter | Default value | Description                                                                                    |
|-----------------|---------------|------------------------------------------------------------------------------------------------|
| `max_runs`      | `50`          | How many test runs to include in the flaky test calculation                                    |
| `threshold`     | `5`           | Threshold of how many test failures out of `max_runs` for a test to be considered flaky        |
| `branch_type`   | `MAINLINE`    | Whether to search for flaky tests only on the mainline branch `MAINLINE` or all branches `ALL` |
| `project`       | `null`        | Name of the project in the repo                                                                |

#### Response fields

| Field name                 | Description                                                |
|----------------------------|------------------------------------------------------------|
| `tests`                    | List of flaky tests                                        |
| `tests.test_case`          | Details about the flaky test                               |
| `tests.failure_count`      | How many times the test failed in the past `max_runs`      |
| `tests.failure_percentage` | Percentage of times the test failed in the past `max_runs` |
| `tests.first_test_case`    | Details about the first failure of the flaky test          |
| `tests.latest_test_case`   | Details about the most recent failure of the flaky test    |

#### Example response

```json
{
    "tests": [
        {
            "test_case": {
                "idx": 2,
                "test_suite_idx": 1,
                "name": "should fail with output",
                "package_name": "projektor.example.spock",
                "test_suite_name": "FailingSpec",
                "class_name": "FailingSpec",
                "file_name": null,
                "duration": 0.006,
                "passed": false,
                "skipped": false,
                "has_system_out_test_case": false,
                "has_system_err_test_case": false,
                "has_system_out_test_suite": true,
                "has_system_err_test_suite": false,
                "public_id": "5EI0XXKNPAYJ",
                "created_timestamp": "2024-04-02T13:51:46.682159",
                "failure": {
                    "failure_message": "Condition not satisfied:\n\nactual == 3\n|      |\n1      false\n",
                    "failure_type": "org.spockframework.runtime.SpockComparisonFailure",
                    "failure_text": "Condition not satisfied:\n\nactual == 3\n|      |\n1      false\n\n\tat projektor.example.spock.FailingSpec.should fail with output(FailingSpec.groovy:22)\n"
                },
                "attachments": null,
                "full_name": "projektor.example.spock.FailingSpec.should fail with output",
                "has_system_out": true,
                "has_system_err": false
            },
            "failure_count": 20,
            "failure_percentage": 40.00,
            "first_test_case": {
                "idx": 2,
                "test_suite_idx": 1,
                "name": "should fail with output",
                "package_name": "projektor.example.spock",
                "test_suite_name": "FailingSpec",
                "class_name": "FailingSpec",
                "file_name": null,
                "duration": 0.006,
                "passed": false,
                "skipped": false,
                "has_system_out_test_case": false,
                "has_system_err_test_case": false,
                "has_system_out_test_suite": true,
                "has_system_err_test_suite": false,
                "public_id": "3P8BEEHBCV8V",
                "created_timestamp": "2024-03-23T13:26:50.797765",
                "failure": {
                    "failure_message": "Condition not satisfied:\n\nactual == 3\n|      |\n1      false\n",
                    "failure_type": "org.spockframework.runtime.SpockComparisonFailure",
                    "failure_text": "Condition not satisfied:\n\nactual == 3\n|      |\n1      false\n\n\tat projektor.example.spock.FailingSpec.should fail with output(FailingSpec.groovy:22)\n"
                },
                "attachments": null,
                "full_name": "projektor.example.spock.FailingSpec.should fail with output",
                "has_system_out": true,
                "has_system_err": false
            },
            "latest_test_case": {
                "idx": 2,
                "test_suite_idx": 1,
                "name": "should fail with output",
                "package_name": "projektor.example.spock",
                "test_suite_name": "FailingSpec",
                "class_name": "FailingSpec",
                "file_name": null,
                "duration": 0.006,
                "passed": false,
                "skipped": false,
                "has_system_out_test_case": false,
                "has_system_err_test_case": false,
                "has_system_out_test_suite": true,
                "has_system_err_test_suite": false,
                "public_id": "5EI0XXKNPAYJ",
                "created_timestamp": "2024-04-02T13:51:46.682159",
                "failure": {
                    "failure_message": "Condition not satisfied:\n\nactual == 3\n|      |\n1      false\n",
                    "failure_type": "org.spockframework.runtime.SpockComparisonFailure",
                    "failure_text": "Condition not satisfied:\n\nactual == 3\n|      |\n1      false\n\n\tat projektor.example.spock.FailingSpec.should fail with output(FailingSpec.groovy:22)\n"
                },
                "attachments": null,
                "full_name": "projektor.example.spock.FailingSpec.should fail with output",
                "has_system_out": true,
                "has_system_err": false
            }
        },
        {
            "test_case": {
                "idx": 1,
                "test_suite_idx": 1,
                "name": "should fail",
                "package_name": "projektor.example.spock",
                "test_suite_name": "FailingSpec",
                "class_name": "FailingSpec",
                "file_name": null,
                "duration": 0.119,
                "passed": false,
                "skipped": false,
                "has_system_out_test_case": false,
                "has_system_err_test_case": false,
                "has_system_out_test_suite": true,
                "has_system_err_test_suite": false,
                "public_id": "5EI0XXKNPAYJ",
                "created_timestamp": "2024-04-02T13:51:46.682159",
                "failure": {
                    "failure_message": "Condition not satisfied:\n\n1 == 2\n  |\n  false\n",
                    "failure_type": "org.spockframework.runtime.SpockComparisonFailure",
                    "failure_text": "Condition not satisfied:\n\n1 == 2\n  |\n  false\n\n\tat projektor.example.spock.FailingSpec.should fail(FailingSpec.groovy:8)\n"
                },
                "attachments": null,
                "full_name": "projektor.example.spock.FailingSpec.should fail",
                "has_system_out": true,
                "has_system_err": false
            },
            "failure_count": 20,
            "failure_percentage": 40.00,
            "first_test_case": {
                "idx": 1,
                "test_suite_idx": 1,
                "name": "should fail",
                "package_name": "projektor.example.spock",
                "test_suite_name": "FailingSpec",
                "class_name": "FailingSpec",
                "file_name": null,
                "duration": 0.119,
                "passed": false,
                "skipped": false,
                "has_system_out_test_case": false,
                "has_system_err_test_case": false,
                "has_system_out_test_suite": true,
                "has_system_err_test_suite": false,
                "public_id": "3P8BEEHBCV8V",
                "created_timestamp": "2024-03-23T13:26:50.797765",
                "failure": {
                    "failure_message": "Condition not satisfied:\n\n1 == 2\n  |\n  false\n",
                    "failure_type": "org.spockframework.runtime.SpockComparisonFailure",
                    "failure_text": "Condition not satisfied:\n\n1 == 2\n  |\n  false\n\n\tat projektor.example.spock.FailingSpec.should fail(FailingSpec.groovy:8)\n"
                },
                "attachments": null,
                "full_name": "projektor.example.spock.FailingSpec.should fail",
                "has_system_out": true,
                "has_system_err": false
            },
            "latest_test_case": {
                "idx": 1,
                "test_suite_idx": 1,
                "name": "should fail",
                "package_name": "projektor.example.spock",
                "test_suite_name": "FailingSpec",
                "class_name": "FailingSpec",
                "file_name": null,
                "duration": 0.119,
                "passed": false,
                "skipped": false,
                "has_system_out_test_case": false,
                "has_system_err_test_case": false,
                "has_system_out_test_suite": true,
                "has_system_err_test_suite": false,
                "public_id": "5EI0XXKNPAYJ",
                "created_timestamp": "2024-04-02T13:51:46.682159",
                "failure": {
                    "failure_message": "Condition not satisfied:\n\n1 == 2\n  |\n  false\n",
                    "failure_type": "org.spockframework.runtime.SpockComparisonFailure",
                    "failure_text": "Condition not satisfied:\n\n1 == 2\n  |\n  false\n\n\tat projektor.example.spock.FailingSpec.should fail(FailingSpec.groovy:8)\n"
                },
                "attachments": null,
                "full_name": "projektor.example.spock.FailingSpec.should fail",
                "has_system_out": true,
                "has_system_err": false
            }
        }
    ],
    "max_runs": 50,
    "failure_count_threshold": 5
}
```

## Changelog

* Projektor server 4.38.0
  * Added repository flaky tests endpoint `GET /api/v1/repo/{org}/{repo}/tests/flaky`
* Projektor server 4.35.0
  * Initial release of API with `GET /api/v1/repo/{org}/{repo}/coverage/current` and `GET /api/v1/org/{org}/coverage/current` endpoints
  * 