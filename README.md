# Dev Blog

A personal developer blog built with [Jekyll](https://jekyllrb.com/) and a customised [Minima v3](https://github.com/jekyll/minima) theme, running fully inside a **dev container** — the authoring environment is version-controlled and portable.

**Live theme:** cyberpunk / hacker-green dark skin with neon accents, animated header, card-style post list, sticky nav, and a subtle grid overlay.

## Features

- 🎨 Custom dark theme — neon green palette, Nunito + Fira Code fonts
- 🔍 Client-side search (instant, no server required)
- 📄 Pagination (6 posts per page via `jekyll-paginate`)
- 🖼️ Per-post cover images via `image:` front matter
- 🔒 Running in a dev container (no local Ruby setup needed)
- 🛡️ Gemfile locked + audited with `bundler-audit`

## Quick start

```bash
# Inside the dev container:
bundle install
bundle exec jekyll serve --host 0.0.0.0 --watch
```

Open <http://localhost:4000> to preview. The server reloads automatically on file changes.

## Writing a post

Add a Markdown file to `_posts/`:

```
_posts/YYYY-MM-DD-your-title.markdown
```

Minimal front matter:

```yaml
---
layout: post
title: "Your Post Title"
date: 2026-01-01 10:00:00 +0000
categories: topic subtopic
tags: ["tag1", "tag2"]
image: /assets/images/cover.jpg   # shown as card image on the home page
---
```

Drop cover images in `assets/images/`. All images currently used are sourced from [Unsplash](https://unsplash.com) under the [Unsplash License](https://unsplash.com/license) — free for personal and commercial use, no attribution required.

## Project structure

```
_config.yml                    # Site config, plugins, pagination settings
_posts/                        # Blog posts (Markdown)
_layouts/home.html             # Custom home layout (search + pagination)
_sass/minima/
  custom-variables.scss        # Color palette, fonts, spacing overrides
  custom-styles.scss           # Theme styles (cards, search, pagination…)
_includes/custom-head.html     # Google Fonts + CSS custom properties
assets/images/                 # Post cover images (Unsplash, free to use)
about.markdown                 # About page
index.html                     # Home page (required name for jekyll-paginate)
```

## Available tasks (VS Code)

| Task | Command |
|------|---------|
| **Jekyll: Serve** | `bundle exec jekyll serve --host 0.0.0.0 --watch` |
| **Jekyll: Build** | `bundle exec jekyll build` |
| **Jekyll: Security Audit** | `bundle-audit update && bundle-audit check` |

## License

Content © Mack. Theme based on [Minima](https://github.com/jekyll/minima) (MIT).

