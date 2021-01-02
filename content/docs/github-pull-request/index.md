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
