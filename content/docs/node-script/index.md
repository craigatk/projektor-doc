---
title: 'Node script'
date: 2020-05-02T19:30:08+10:00
draft: false
weight: 11
summary: Node script for publishing Javascript results to Projektor
---

Node script for publishing test results from your Javascript project to the
Projektor server.

## Installation

Add the NPM package as a dev dependency with either NPM:

`npm install --save-dev projektor-publish`

or Yarn

`yarn add -D projektor-publish`

## Usage

Then run this script to publish test results to the Projektor server:

`yarn projektor-publish --serverUrl=<projektor_url> <file_globs>`

You can include multiple file globs, such as:

`yarn projektor-publish --serverUrl=<url> ui/test-results/*.xml ui/cypress/*.xml`

Another configuration option is to store the config in `projektor.json` in the root of your project.
Then you don't need to specify the parameters on the command line each time.

```
{
  "serverUrl": "<url>",
  "results": [
    "ui/test-results/*.xml",
    "ui/cypress/*.xml"
  ]
}
```

You can also customize the name of the config file by passing the `--configFile=<path>` flag:

`yarn projektor-publish --configFile=projektor.cypress.json`

## Code coverage

Projektor can show code coverage stats alongside test results.

![Projektor Jest code coverage](/images/jest-coverage.png "Projektor Jest code coverage")

Starting with version `2.7.0` you can specify the paths to the Jest coverage XML files
in the `coverage` parameter.

For example, in the config file:

```
{
  "serverUrl": "<url>",
  "results": [
    "ui/test-results/*.xml"
  ],
  "coverage": [
     "ui/coverage/*.xml"
  ]
}
```

Or on the command line:

`yarn projektor-publish --serverUrl=<projektor_url> --coverage="ui/coverage/*.xml" ui/test-results/*.xml`

## Attachments

You can also attach files to the test results, which can be helpful to attach things like screenshots
from Cypress tests to debug test failures in CI where you can't watch the test run.

Use the `attachments` config value to tell Projektor which files to attach.

For example, from the config file:

```
{
  "serverUrl": "<url>",
  "results": [
    "ui/test-results/*.xml",
    "ui/cypress/*.xml"
  ],
  "attachments": [
     "ui/cypress/screenshots/*.png"
  ]
}
```

Or from the command line: 

`yarn projektor-publish --serverUrl=<projektor_url> --attachments="ui/cypress/screenshots/*.png" ui/cypress/*.xml`

## Performance test results

Projektor can show individual performance test results and graph performance results over time.

Use the `performance` config parameter to pass performance results to Projektor:

`yarn projektor-publish --serverUrl=<projektor_url> --performance=perf-results/*.json`

## Exit code when there is a test failure

When running in CI systems it can be handy to combine the execution of tests like Cypress with
the publishing of results to Projektor by using the "or" `||` operator.
Then the results will be published even when the tests fail if the CI system would normally stop the build execution when the tests fail.

For example, `yarn cy:run || yarn projektor-publish`

A downside of this approach is that due to the `||` the exit code is `0` and the CI build won't stop after publishing the test results,
as you'd probably want it to when tests fail.

To support using this approach but stopping the CI build from proceeding if there is a test failure, you can use the `exitWithFailure` configuration flag.

For example, if configuring Projektor via the command line: `yarn cy:run || yarn projektor-publish --serverUrl=<projektor_url> --exitWithFailure <file_globs>`

Or in the configuration file:

```
{
  "serverUrl": "<url>",
  "results": [
    "ui/test-results/*.xml"
  ],
  "exitWithFailure": true
}
```

## Slack message

It can be helpful to proactively notify folks when a CI build fails via Slack.
To help make that easier, the Projektor Node script has the option to write a formatted Slack
message in a JSON file. This Slack message includes a direct link to the Projektor test report
so users can quickly investigate test failures.

To use it, set the `writeSlackMessageFile` parameter and set the `slackProjectName` parameter to the name of your
project - it is included in the Slack message.

For example:

`yarn projektor-publish --serverUrl=<url> --writeSlackMessageFile --projectName=<project-name> ui/test-results/*.xml`

### Code quality reports

Starting with Node script `3.9.0`,
Projektor supports any and all code quality tools (linting, static analysis, etc.) that can output their results in text files.

To be able to see the code quality reports in your Projektor build,
pass the paths to the reports in the `codeQuality` config parameter.

For example:

`yarn projektor-publish --serverUrl=<url> --codeQuality=ui/reports/*.txt ui/test-results/*.xml`

## All configuration options

| Parameter             | Type             | Default                          | Description                                |
| --------------------- | ---------------- | -------------------------------- | ------------------------------------------ |
| serverUrl**           | `string`         | `null`                           | Projektor server URL to publish results to |
| results               | `Array<string>`  | `[]`                             | Paths to the test results XML files |
| coverage              | `Array<string>`  | `[]`                             | Paths to the code coverage XML files to include with the report |
| attachments           | `Array<string>`  | `[]`                             | Paths to the files to attach to the test report |
| performance           | `Array<string>`  | `[]`                             | Paths to performance test results files to send to Projektor |
| codeQuality           | `Array<string>`  | `[]`                             | Paths for any code quality report files you want to send to Projektor |
| exitWithFailure       | `boolean`        | `false`                          | After publishing exit with a non-zero exit code if there is a test failure |
| failOnPublishError    | `boolean`        | `false`                          | Exit with a non-zero exit code if the server returns an error when publishing results |
| writeSlackMessageFile | `boolean`        | `false`                          | Writes a Slack message JSON file with a link to the Projektor test report that you can then publish to Slack |
| slackProjectName      | `string`         | `null`                           | Name of the project to include in the Slack message file. |
| slackMessageFileName  | `string`         | `projektor_failure_message.json` | Name of the Slack message file, if enabled |
| repositoryName        | `string`         | `null`                           | Pass the name of the Git repository in `org/repo` format, useful if running outside CI or Projektor isn't able to auto-detect the repository |
| projectName           | `string`         | `null`                           | Differentiate different projects in the same repo. Will also be used as the name of the project in the Slack message file if `slackProjectName` is not set. |
| resultsMaxSizeMB      | `string`         | `20`                             | Max size of the combined test results payload, in MB. Uploading the results will fail if the combined test results payload exceeds this size. |
| attachmentMaxSizeMB   | `string`         | `20`                             | Max size of individual attachments, in MB. Uploading an individual attachment will fail if the attachment exceeds this size. |
| gitMainBranchNames    | `string`         | `main,master`                    | Comma-separated list of the mainline branches for this repo. Mainline branches are used when calculating things like current code coverage for the repo. |

** _Required_

## Changelog

* 3.10.0
  *  Can publish code coverage results by themselves without any test results files
* 3.9.0
  * Adding support for code quality files
* 3.8.0
  * Write specific Slack message file if no results found 
* 3.7.0
  * Adding flag to fail if publish error from server
* 3.6.0
  * Making Git main branch names configurable with new `gitMainBranchNames` param 
* 3.5.0
  * Adding `slackProjectName` param for specific Slack message file
* 3.4.3
  * Write passing Slack message file in Node script
* 3.4.2
  * Include coverage file paths in log message
* 3.4.1
  * Updating lodash dependency version
* 3.4.0
  * Adding group field for appending results
* 3.3.0
  * Adding support for configurable results max body size with default 20MB
* 3.2.0
  * Making attachment max size configurable in Node script and defaulting it to 20 MB
* 3.1.2
  * Adding Drone Git environment variables to Node script
* 3.1.1
  * Updating to axios 0.21.1
* 3.1.0
  * Passing pull request number from Node script
* 3.0.0
  * Sending coverage along with results and including commit SHA
* 2.13.1
  * Adding base path to coverage in Node script
* 2.13.0
  * Adding base path to coverage in Node script
* 2.12.0
  * Logging error message from Projektor server when sending results or coverage fails
* 2.11.0
  * Adding support for passing repository name
* 2.10.0
  * Adding support for publishing performance results
* 2.9.0
  * Adding compression support when sending test results
* 2.8.0
  * Including whether build was run in CI to support test run duration graph and flaky test detection
* 2.7.0
  * Added support for code coverage stats
* 2.3.1
  * Now log a message if no test results are found in the configured location(s)
* 2.3.0
  * Added Slack message file support
* 2.2.0
  * Added `exitWithFailure` configuration option
