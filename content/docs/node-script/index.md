---
title: 'Node script'
date: 2020-05-02T19:30:08+10:00
draft: false
weight: 3
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

To use it, set the `writeSlackMessageFile` parameter and set the `projectName` parameter to the name of your
project - it is included in the Slack message.

For example:

`yarn projektor-publish --serverUrl=<url> --writeSlackMessageFile --projectName=<project-name> ui/test-results/*.xml`

## All configuration options

| Parameter             | Type             | Default                          | Description                                |
| --------------------- | ---------------- | -------------------------------- | ------------------------------------------ |
| serverUrl**           | `string`         | `null`                           | Projektor server URL to publish results to |
| results**             | `Array<string>`  | `[]`                             | Paths to the test results XML files |
| attachments           | `Array<string>`  | `[]`                             | Paths to the files to attach to the test report |
| exitWithFailure       | `boolean`        | `false`                          | After publishing exit with a non-zero exit code if there is a test failure |
| writeSlackMessageFile | `boolean`        | `false`                          | Writes a Slack message JSON file with a link to the Projektor test report that you can then publish to Slack |
| projectName           | `string`         | `null`                           | Name of the project to include in the Slack message file |
| slackMessageFileName  | `string`         | `projektor_failure_message.json` | Name of the Slack message file, if enabled |

** _Required_

## Changelog

* 2.3.1
  * Now log a message if no test results are found in the configured location(s)
* 2.3.0
  * Added Slack message file support
* 2.2.0
  * Added `exitWithFailure` configuration option
