---
title: 'Slack notifications'
date: 2026-10-14T19:30:08+10:00
draft: false
weight: 8
summary: Get notified in Slack when your mainline build goes red, coverage drops, or a test becomes flaky
---

**Alpha:** Slack notifications are an alpha feature. The configuration and message format may change in future releases.

Projektor can post a message to a Slack channel when a CI build on your repository's mainline branch
needs attention, so your team finds out about problems without having to go look for them.

Projektor sends a notification when a mainline CI build:

* **Goes red** - the build failed and the previous mainline build passed (or there was no previous build).
  Projektor only notifies when mainline changes from passing to failing, not on every failing build.
* **Drops code coverage** - line coverage decreased compared to the previous mainline build by at least
  the coverage drop threshold (0.1 percentage points by default).
* **Makes a test flaky** - a test failure in this build is the one that reaches the
  [flaky test](/docs/flaky-test/) threshold (5 failures in the last 50 mainline builds by default).

If a single build triggers more than one of these, Projektor combines them into one message.
Each message links to the Projektor report and includes the branch and commit.

These notifications are sent by the Projektor server. They're separate from the
[Node script's Slack message file](/docs/node-script/#slack-message) option, which writes a Slack message
to a JSON file during your build for your CI job to post itself.

## Setting up Slack notifications

### 1. Create a Slack app

1. Create a [Slack app](https://api.slack.com/apps) in your Slack workspace.
2. Under **OAuth & Permissions**, add the `chat:write` bot token scope.
3. Install the app to your workspace and copy the **Bot User OAuth Token** (it starts with `xoxb-`).
4. Invite the app's bot user to each channel that should receive notifications, for example with `/invite @your-app-name`.

### 2. Configure the Projektor server

Set the following environment variables on the [Projektor server](/docs/server/):

```
SLACK_API_URL=<base URL of the Slack Web API, https://slack.com/api>
SLACK_BOT_TOKEN=<bot token of the Slack app, xoxb-...>
SERVER_BASE_URL=<base URL of the Projektor server, used for the links in the notification>
```

Slack notifications, and the channel setting in the Projektor UI, are only enabled when both `SLACK_API_URL` and `SLACK_BOT_TOKEN` are set.

You can also tune when notifications are sent with these optional environment variables:

| Environment variable            | Default | Description                                                                         |
|---------------------------------|---------|-------------------------------------------------------------------------------------|
| `SLACK_COVERAGE_DROP_THRESHOLD` | `0.1`   | Minimum drop in line coverage, in percentage points, to notify about                |
| `SLACK_FLAKY_MAX_RUNS`          | `50`    | Number of recent mainline builds to look at when finding flaky tests                |
| `SLACK_FLAKY_FAILURE_THRESHOLD` | `5`     | Number of failures in those builds for a test to be considered flaky               |

### 3. Pick a channel for your repository

Each repository is mapped to its own Slack channel. For mono-repos, each project within the repository
can have its own channel.

1. Open any Projektor test report for the repository (or project) that includes Git repository information.
2. In the report's dashboard, find the **Slack channel** item and click **Set channel**.
3. Enter the channel name, for example `build-alerts`. You can include the leading `#` or leave it off.

When you save the channel, Projektor posts a confirmation message to it.
If Projektor can't post to the channel, for example because the channel name is mistyped or
the bot hasn't been invited to it, Projektor shows the error and doesn't save the channel.

To change or remove the channel later, click **Edit** next to the channel name in the report dashboard.

Repositories and projects without a channel don't get notifications.

## Requirements

Projektor only sends Slack notifications for test results that:

* were published from a CI build
* include Git repository information
* are from a mainline branch of the repository

By default, Projektor treats `main` and `master` as the mainline branches. If your repository uses a different
mainline branch (such as `develop`), set the `gitMainBranchNames` config param in the
[Gradle plugin](/docs/gradle-plugin/#all-configuration-options) or [Node script](/docs/node-script#all-configuration-options).
