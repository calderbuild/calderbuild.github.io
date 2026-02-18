# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Minimal Jekyll blog with Cyberpunk Brutalism design. Live at https://calderbuild.github.io.

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

- `default.html` -- site shell: header nav, `<main>{{ content }}</main>`, footer. Includes Google Analytics, Open Graph/Twitter meta, JSON-LD structured data, font loading.
- `post.html` -- article template with schema.org markup, prev/next navigation. Has its own `<style>` block for post-specific styles.
- `page.html` -- simple title + content wrapper. Also has a page-specific `<style>` block.

### Pages and Routing

- `index.html` -- homepage: hero section, featured projects (`site.data.projects limit:3`), latest posts (`site.posts limit:6`). No pagination.
- `blog/index.html` -- paginated blog listing using `paginator.posts`. This file MUST stay at `blog/index.html` (not root) for `paginate_path: "/blog/page:num/"` to work.
- `projects.html` (`permalink: /projects/`) -- full project listing from `_data/projects.yml`.
- `about.md` (`permalink: /about/`) -- uses `page` layout.
- `archive.html` -- non-paginated chronological post list.

### Data Files

`_data/projects.yml` -- structured project data (name, tagline, description, stars, award, tech, url, category). Used by both `index.html` (featured projects, limit:3, filtered by `project.url`) and `projects.html` (full listing, shows "Private Project" for null URLs).

### CSS Architecture

Single stylesheet at `css/style.css` using CSS custom properties (`:root` vars). Individual pages add page-specific styles via inline `<style>` blocks at the bottom of their HTML files (`index.html`, `blog/index.html`, `post.html`, `page.html`, `projects.html`).

Key design tokens: colors (`--bg-primary: #0a0a0f`, `--accent-cyan: #00ffff`, `--accent-magenta: #ff00ff`), fonts (`--font-display: Space Mono`, `--font-body: IBM Plex Sans`, `--font-mono: JetBrains Mono`), spacing (`--space-xs` through `--space-3xl`), effects (`--glow-cyan`, `--border-glow`).

No JavaScript in the codebase. Mobile nav toggle uses inline `onclick` to flip a CSS class.

### Pagination

`jekyll-paginate` only works on `blog/index.html` (the file with `paginate_path` matching). The homepage (`index.html`) uses `site.posts limit:6` directly -- it does not paginate. Post permalinks follow `/blog/:year/:month/:day/:title/` from `_config.yml`.

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
