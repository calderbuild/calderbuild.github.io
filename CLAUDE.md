# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Minimal Jekyll blog with Cyberpunk Brutalism design. Live at https://jasonrobert.me.

## Development Commands

```bash
bundle install              # Install dependencies
bundle exec jekyll serve    # Local dev server (localhost:4000)
bundle exec jekyll build    # Production build to _site/
```

### Custom Slash Commands

- `/deploy` - Commit and push important files to GitHub (excludes docs/tests)
- `/arrange` - Delete unnecessary files (preserves .claude/ and result_seo/)

## Architecture

### Layout Chain

`default.html` is the root layout. Both `post.html` and `page.html` extend it via `layout: default`.

- `default.html` -- site shell: header nav, `<main>{{ content }}</main>`, footer. Includes Google Analytics, Open Graph/Twitter meta, font loading.
- `post.html` -- article template with schema.org markup, prev/next navigation. Has its own `<style>` block for post-specific styles.
- `page.html` -- simple title + content wrapper. Also has a page-specific `<style>` block.

### Data Files

`_data/projects.yml` -- structured project data (name, tagline, description, stars, award, tech, url). Used by both `index.html` (featured projects) and `projects.html` (full listing).

### CSS Architecture

Single stylesheet at `css/style.css` using CSS custom properties (`:root` vars). Individual layouts and pages add page-specific styles via inline `<style>` blocks at the bottom of their HTML files (`index.html`, `blog.html`, `post.html`, `page.html`, `projects.html`).

Key design tokens are defined as CSS custom properties: colors (`--bg-primary`, `--accent-cyan`), fonts (`--font-display`, `--font-body`, `--font-mono`), spacing (`--space-xs` through `--space-3xl`), effects (`--glow-cyan`, `--border-glow`).

### Pagination

`jekyll-paginate` only works on `blog.html` (the file with `paginate_path` matching). The homepage (`index.html`) uses `site.posts limit:6` directly -- it does not paginate. The homepage also renders featured projects from `site.data.projects limit:3`.

### Build Constraints

The `Gemfile` uses the `github-pages` gem, which pins Jekyll and all plugin versions to match GitHub Pages. Do not add gems outside the [GitHub Pages dependency list](https://pages.github.com/versions/). The `future: true` config flag means future-dated posts are published.

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

## Deployment

Push to `master` triggers GitHub Actions (`.github/workflows/jekyll.yml`). Ruby 3.1, auto-deploys to GitHub Pages. No manual build step needed.

### Plugins

- `jekyll-paginate` -- blog pagination
- `jekyll-seo-tag` -- SEO meta tags (auto-injected)
- `jekyll-sitemap` -- auto-generated sitemap.xml
