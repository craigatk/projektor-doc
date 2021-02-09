---
title: 'Jest tests'
date: 2021-02-08T19:30:08+10:00
draft: false
weight: 9
summary: Configuring Jest tests to write results for Projektor 
---

Projektor can visualize test results from any test framework that can output JUnit XML format,
including Jest. In just a few simple steps you can set up your Jest test results to be available in Projektor.

First, add the `jest-junit` package (https://www.npmjs.com/package/jest-junit) to your project:

```
  "devDependencies": {
    "jest-junit": "12.0.0",
  }
```

Then configure the `jest-junit` package to write the JUnit XMl results file that Projektor understands:

`package.json`
```
  "jest-junit": {
    "includeConsoleOutput": "true",
    "outputDirectory": "jestResults",
    "classNameTemplate": "{classname}",
    "usePathForSuiteName": "true"
  }
```

With the above config, the `jest-junit` package will write the JUnit XML file in the `jestResults`
directory, so now we just need to point Projektor at that directory.

On the command line:

`yarn projektor-publish --serverUrl=<projektor_url> jestResults/*.xml`

Or in the `projektor.json` configuration file:

```
{
  "serverUrl": "<url>",
  "results": [
    "jestResults/*.xml",
  ]
}
```
