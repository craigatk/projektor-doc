---
title: 'Code quality'
date: 2021-10-11T19:30:08+10:00
draft: false
weight: 8
summary: View code quality reports in Projektor
---

There are a variety of code quality tools out there, such as linters and static analysis tools.
These tools can help enforce common formatting, find potential bugs, and more.

When code quality tools encounter a problem in your codebase, they can fail
the build until the issue is resolved.
Sifting through build output to find the specific code quality failures can be a pain,
so you can view those code quality failures directly in Projektor - making them faster and easier to diagnose and fix.

![Code quality report](/images/code-quality/code-quality-report.png "Code quality report")

To view the code quality reports in your build, click on the "Code quality" link in the sidebar menu.

## Publishing code quality reports

To work with the widest variety of code quality tools, Projektor supports any
code quality report text file.

To publish code quality reports to Projektor, please see the publishing config for:

* [Gradle plugin](../gradle-plugin/#code-quality-reports)
