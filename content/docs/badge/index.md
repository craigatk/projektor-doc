---
title: 'Readme badges'
date: 2021-01-02T19:30:08+10:00
draft: false
weight: 7
summary: Badge for project readme with code coverage stats
---

Badges in your project's readme can be a great way to give viewers a quick high-level
look at some of your project's key stats.

## Coverage

You can display the code coverage percentage for your project directly
on your project's readme page with Projektor.

![Code coverage badge readme](/images/badge/code-coverage-badge-readme.png "Code coverage badge readme")

The Markdown code to display the badge is available in a couple places in Projektor.
Click on the copy icon to copy the Markdown code to your clipboard to paste into your project's readme.

On a test report page:

![Test run coverage badge](/images/badge/test-run-coverage-badge.png "Test run coverage badge")

And on the coverage page for the repository:

![Repository coverage badge](/images/badge/repo-code-coverage-badge.png "Repository coverage badge")

Then paste the copied Markdown code into your project's Markdown readme file.

### Configure branches used for coverage badge

Projektor uses the coverage from most recent mainline branch when displaying the code coverage badge 
so it only includes reviewed / merged code instead of code from pull request branches.

By default, Projektor considers "main" and "master" as mainline branches, but that can be configured 
if a specific repo uses different branch(es) for the mainline branch (such as "develop").
See the `gitMainBranchNames` config param in the [Gradle plugin](/docs/gradle-plugin/#all-configuration-options) or [Node script](/docs/node-script#all-configuration-options) docs.
