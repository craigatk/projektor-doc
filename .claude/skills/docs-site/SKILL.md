---
name: docs-site
description: Use when adding, editing, or reviewing pages on the Projektor documentation site (this repo, projektor-doc) — a Hugo static site deployed to Netlify at projektor.dev. Covers the content/docs/<slug>/index.md page convention and frontmatter fields, weight-based nav ordering, existing style conventions (tables, changelog sections, live.projektor.dev example links), where images and the mermaid shortcode live, and local preview/build commands.
user-invocable: true
---

# Working on the Projektor docs site

This repo (`projektor-doc`) is a [Hugo](https://gohugo.io/) static site using the
`hugo-whisper-theme`, deployed to Netlify at projektor.dev. It documents the `projektor` repo,
which is a **sibling directory** (`../projektor`) — when documenting a feature, verify behavior
against that repo's actual source rather than guessing, the same way you'd check any other
codebase before writing docs for it.

## Adding or editing a page

Each doc page is `content/docs/<slug>/index.md`. Frontmatter convention (see any existing page,
e.g. `content/docs/api/index.md`):

```yaml
---
title: 'Page title'
date: 2020-05-02T19:30:08+10:00
draft: false
weight: 10
summary: One-line summary shown on the docs index page
---
```

- `weight` controls ordering in the docs nav — lower comes first. Check existing weights before
  picking one (`grep -h weight content/docs/*/index.md`); several pages already share a weight
  (e.g. `ai` and `badge` are both `7`), so a collision isn't fatal, but prefer the next unused
  value at the end unless the page genuinely belongs earlier in the flow.
- `date` uses the `+10:00` offset format seen throughout, for consistency with existing pages —
  match it rather than using local time.
- `summary` is used for the content summary on the docs homepage.
- `mermaid: true` in frontmatter enables the mermaid shortcode (see
  `content/docs/introduction/index.md` and `layouts/shortcodes/mermaid.html`) if a page needs a
  diagram.

## Style conventions to follow

- `##`/`###` section headers; `####` for "Parameters" / "Response fields" / "Example response"
  subsections within a feature.
- Parameter and response field tables use backtick-quoted field names in the first column, e.g.:

  ```markdown
  | Field name | Description |
  |------------|-------------|
  | `id`       | ...         |
  ```

- Prefer real, working example URLs against the live demo instance,
  `https://live.projektor.dev/...`, over placeholders — e.g.
  `https://live.projektor.dev/api/v1/repo/craigatk/projektor/coverage/current`. If you can, hit
  the real endpoint/tool and paste its actual response rather than hand-writing one.
- Versioned/API-like features (see `content/docs/api/index.md`) get a trailing `## Changelog`
  section listing what was added in each server version, and inline "Available in Projektor
  server versions >=X.Y.Z" notes next to each capability. Only assert a version number if you've
  actually confirmed the feature shipped in a tagged release (check `git tag` /
  `CHANGELOG.md` in `../projektor`) — don't back-fill a version number for something still
  unreleased.

## Images

Static images live under `static/images/<page-slug>/`, one directory per doc page, and are
referenced with an absolute path plus title text:

```markdown
![Failing Cypress test](/images/ai/cypress-failing-test-ai.png "Failing Cypress test")
```

## Local preview and build

| Goal | Command |
|---|---|
| Live-reload local preview | `hugo serve` |
| Full build (catches template/content errors) | `hugo` |

`public/` and `resources/_gen` are gitignored build output — don't commit them. Netlify builds
with `HUGO_VERSION = "0.69.2"` (pinned in `netlify.toml`); if a local `hugo` is a much newer
major version, layout behavior can drift from what actually deploys, so treat unexpected local
build warnings with that in mind.
