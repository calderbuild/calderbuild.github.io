# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal site for Calder, an AI agent engineer. Live at https://calderbuild.github.io.

## Development Commands

```bash
bundle install              # Install dependencies
bundle exec jekyll serve    # Local dev server (localhost:4000)
bundle exec jekyll build    # Production build to _site/
JEKYLL_ENV=production bundle exec jekyll build  # Validate production build locally
```

Always run `bundle exec jekyll build` before pushing. For layout/content changes, run `serve` and verify `/`, `/blog/`, `/projects/`, `/about/`, `/archive/`, and at least one post page.

### Custom Slash Commands

- `/deploy` - Commit and push important files to GitHub (excludes docs/tests)
- `/arrange` - Delete unnecessary files (preserves .claude/ and result_seo/)

## Architecture

### Layout Chain

`default.html` is the root layout. Both `post.html` and `page.html` extend it via `layout: default`.

- `default.html` -- site shell: skip link, header nav (Projects, Writing only when posts exist, About), `<main>`, footer built from `site.links`. Loads fonts, GA, `{% seo %}`.
- `post.html` -- article: date, title, subtitle, tags (link to `/tags/#slug`), `.prose` body, prev/next.
- `page.html` -- title + optional `lede` front matter + `.prose` body (used by `about.md`).

Shared partials live in `_includes/`: `work.html` (project rows, takes `projects=`), `no-posts.html` (empty state for blog/archive/tags).

### Pages and Routing

- `index.html` -- homepage: claim + "reasoning trace" hero (each evidence line links to its proof), Now, selected work (`featured: true` projects), latest posts only if any exist.
- `blog/index.html` -- paginated post list. MUST stay at `blog/index.html` for `paginate_path: "/blog/page:num/"` to work.
- `projects.html` (`/projects/`) -- featured projects, smaller tools, and the hackathon log table (`#hackathons`).
- `about.md` (`/about/`) -- experience, research (`#research`), awards, contact.
- `archive.html`, `tags/index.html` -- chronological list and tag-grouped list; both show the empty state when there are no posts. There are no per-tag pages.

### Data Files

- `_data/projects.yml` -- `name, problem, what, proof, stars, tech, url, demo, featured`. Rows render problem first, then what it does, then proof. `url: null` shows "Code not public".
- `_data/hackathons.yml` -- `event, project, url, result`. The homepage counts entries and entries with a `result`, so keep it to real, named events.

Every number on the site must trace to a source (GitHub, an award record, the paper). Refresh stars with `gh repo list calderbuild --json name,stargazerCount`.

### CSS Architecture

Single stylesheet `css/style.css`, no per-page `<style>` blocks. Tokens in `:root` with a `prefers-color-scheme: dark` override: `--paper #EEF1F3`, `--ink #11171D`, `--muted`, `--rule`, `--marker #FFE45C` (the only accent: the `.mark` highlight, used in the hero trace only), `--link #1F5FD1`. Fonts: Schibsted Grotesk (display), Newsreader (body), IBM Plex Mono (trace, dates, data). No JavaScript except GA.

### Posts

All 2025 posts are `published: false`: they quoted numbers that could not be backed up (2026-10-02). Do not republish them without rewriting every claim against a source.

### Pagination

`jekyll-paginate` only works on `blog/index.html` (the file with `paginate_path` matching). The homepage (`index.html`) uses `site.posts limit:4` directly -- it does not paginate. Post permalinks follow `/blog/:year/:month/:day/:title/` from `_config.yml`.

### Build Constraints

The `Gemfile` uses the `github-pages` gem, which pins Jekyll and all plugin versions to match GitHub Pages. Do not add gems outside the [GitHub Pages dependency list](https://pages.github.com/versions/). The `future: true` config flag means future-dated posts are published. `_config.yml` excludes `CLAUDE.md` from the built site.

## Coding Style

- 2-space indentation in HTML, CSS, and YAML front matter
- `kebab-case` for page and asset filenames
- Match existing patterns in `css/style.css`; keep selectors descriptive and grouped by section

## Content Guidelines

- **No emojis** in any content, code comments, or documentation
- Author name: "Calder" everywhere
- Posts are English-only

### Blog Post Front Matter

Posts go in `_posts/` with filename `YYYY-MM-DD-title.md`.

```yaml
---
layout: post
title: "Post Title"
subtitle: "Optional subtitle"
description: "SEO meta description"
date: YYYY-MM-DD HH:MM:SS
updated: YYYY-MM-DD HH:MM:SS  # optional
author: "Calder"
header-img: "img/post-bg-*.jpg"  # optional
tags:
  - Tag1
  - Tag2
---
```

## Commits

Conventional Commit style: `feat:`, `fix:`, `refactor:`, `perf:`, `chore:`, `docs:`. Scopes optional (e.g., `fix(seo): ...`). Keep commits atomic.

## Deployment

Push to `master` triggers GitHub Actions (`.github/workflows/jekyll.yml`). Ruby 3.1, auto-deploys to GitHub Pages. No manual build step needed.

### Plugins

- `jekyll-paginate` -- blog pagination
- `jekyll-seo-tag` -- SEO meta tags (auto-injected)
- `jekyll-sitemap` -- auto-generated sitemap.xml
- `jekyll-feed` -- auto-generated Atom feed at `/feed.xml`
