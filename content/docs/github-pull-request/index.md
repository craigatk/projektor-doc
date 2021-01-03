---
title: 'GitHub PR comments'
date: 2021-01-02T19:30:08+10:00
draft: false
weight: 6
summary: Quickly access Projektor reports through automatic GitHub pull request comments
---

Projektor can automatically add a comment to your pull requests to make it faster and easier to view the
test reports for your pull requests.

The pull request comment has the following:

* Link to the Projektor test report
* Whether the tests passed or failed
* Test count (passed and failed)
* Code coverage percentage (if project uses code coverage)
* Date report created

![GitHub pull request comment](/images/github-pull-request/pull-request-comment.png "GitHub pull request comment")

Projektor will update the pull request comment with additional test reports when the code pull request is updated
and more builds run.

## Code coverage delta

To quickly show whether a pull request increases or decreases the code coverage in a project,
Projektor will include the delta in coverage percentage in the pull request comment.

For example, a pull request that decreases the coverage (likely a red flag on the change):

![GitHub pull request comment reduced code coverage](/images/github-pull-request/pr-comment-decreased-coverage.png "GitHub pull request comment reduced code coverage")

Or on the positive side, a pull request that increases the code coverage:

![GitHub pull request comment increased code coverage](/images/github-pull-request/pr-comment-increased-coverage.png "GitHub pull request comment increased code coverage")

## Requirements

### Publishing plugin

For the pull request comments you'll need to be using at least the following versions of the Projektor publishing plugins:

* Gradle plugin `7.1.0`+
* Node script `3.1.0`+

### GitHub app

You'll also need to install Projektor as a GitHub app in your GitHub organization or project 
to give Projektor access to your project's pull requests to make the comments.
Projektor needs read/write access to pull requests and issues to add/update its comments,
but it doesn't need access to your source code.
