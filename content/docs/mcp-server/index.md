---
title: 'MCP server'
date: 2026-07-18T19:30:08+10:00
draft: false
weight: 4
summary: Query Projektor test failure data from AI coding assistants via its MCP server
---

Projektor runs a [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server, so AI coding
assistants like Claude Code can pull test failure diagnostics directly from Projektor while working in
your codebase — for example, asking "why is this pull request's test failing?" and getting back the
actual failure message and stack trace instead of having to go find it manually.

## Endpoint

The MCP server is served from your Projektor server at:

`/mcp`

For example: `https://live.projektor.dev/mcp`

It speaks the MCP [Streamable HTTP transport](https://modelcontextprotocol.io/docs/concepts/transports)
and doesn't require any authentication, the same trust model as the rest of Projektor's REST API.

## Connecting a client

With the Claude Code CLI:

```
claude mcp add --transport http projektor https://live.projektor.dev/mcp
```

(substitute your own Projektor server's URL). Other MCP clients that support a remote HTTP server can
be pointed at the same URL, typically by adding an entry like this to their MCP server config:

```json
{
  "mcpServers": {
    "projektor": {
      "url": "https://live.projektor.dev/mcp"
    }
  }
}
```

## Tools

### get_pull_request_failing_test_context

Fetches the diagnostic context (failure message, stack trace, stdout/stderr) for every currently-failing
test in the most recent Projektor test run recorded for a GitHub pull request, so it can be used to
diagnose why those tests are failing.

#### Parameters

| Parameter            | Description                                                                                          |
|-----------------------|-------------------------------------------------------------------------------------------------------|
| `pullRequestUrl`      | The GitHub pull request's URL, e.g. `https://github.com/{org}/{repo}/pull/{number}`. An alternative to passing `orgName`, `repoName`, and `pullRequestNumber` separately. |
| `orgName`             | GitHub organization or user name that owns the repository                                             |
| `repoName`            | GitHub repository name                                                                                |
| `pullRequestNumber`   | The pull request number                                                                               |

Identify the pull request either with `pullRequestUrl`, or with `orgName`, `repoName`, and
`pullRequestNumber` together.

#### Example question

With the MCP server connected, you can ask your assistant in plain language, e.g.:

> Why is this pull request's test failing: https://github.com/craigatk/projektor/pull/2543

The assistant will call `get_pull_request_failing_test_context` with that URL, get back the failing
test's name, failure message, and stack trace, and use that to explain the failure instead of asking you
to go dig it out of a CI log.

#### Example response

```json
{
  "test_run_public_id": "THEOQRSBH8DB",
  "failing_test_cases": [
    {
      "test_name": "should list code quality reports on code quality page.code quality should list code quality reports on code quality page",
      "debug_context_markdown": "# Test Failure: code quality should list code quality reports on code quality page\n\n- **Full name:** should list code quality reports on code quality page.code quality should list code quality reports on code quality page\n- **Suite:** code quality\n- **Class:** should list code quality reports on code quality page\n- **File:** cypress/e2e/code_quality.cy.js\n- **Duration:** 0.000 ms\n\n## Failure\n\n**Type:** AssertionError\n\n**Message:**\n```\nTimed out retrying after 10000ms: expected '<span>' to contain 'github-line-67'\n```"
    }
  ]
}
```

If no test run is found for the given pull request, the tool returns a message saying so instead of an
error.
