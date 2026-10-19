# Projektor documentation site

Source for [projektor.dev](https://projektor.dev), the documentation site for the
[Projektor test results viewer](https://github.com/craigatk/projektor).

Built with [Hugo](https://gohugo.io/) and the [Whisper theme](https://themes.gohugo.io/hugo-whisper-theme),
and deployed to [Netlify](https://www.netlify.com/).

## Running locally

Install [Hugo](https://gohugo.io/installation/) (0.120 or newer; Netlify builds with 0.168.0, pinned in
`netlify.toml`), then from the repo root:

| Goal | Command |
|------|---------|
| Live-reload preview at http://localhost:1313 | `hugo serve` |
| Full build into `public/` (catches template and content errors) | `hugo` |

`public/` and `resources/_gen/` are build output and are gitignored.

## Project layout

| Path | Contents |
|------|----------|
| `content/docs/<slug>/index.md` | One directory per documentation page |
| `static/images/<slug>/` | Images for each page, referenced as `/images/<slug>/<file>` |
| `layouts/` | Overrides of the theme's templates, plus the `details` and `mermaid` shortcodes |
| `themes/hugo-whisper-theme/` | The Whisper theme (vendored) |
| `config.toml` | Site configuration, top menu, and homepage content |
| `netlify.toml` | Netlify build settings |

## Adding a page

Create `content/docs/<slug>/index.md` with frontmatter like:

```yaml
---
title: 'Page title'
date: 2020-05-02T19:30:08+10:00
draft: false
weight: 10
group: 'Features'
summary: One-line summary shown on the docs index page
---
```

- `group` places the page in the docs nav and must be one of the groups in
  `layouts/partials/sidebar.html`: `Getting started`, `Features`, `Publishing results`, or `Server & API`.
  Pages without a group don't appear in the nav.
- `weight` orders pages in the nav (lower first). Each page uses a unique weight; check existing ones with
  `grep -h weight content/docs/*/index.md`.
- Add `mermaid: true` to use the `mermaid` diagram shortcode, and wrap long, skippable content in
  `{{< details summary="..." >}}` ... `{{< /details >}}`.

## Deployment

The site is built and deployed by Netlify using the settings in `netlify.toml`.
