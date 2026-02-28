---
layout: post
title: "Setting Up a Jekyll Blog in a Dev Container"
date: 2026-02-28 19:30:15 +0000
categories: jekyll devcontainers
tags: [jekyll, docker, devcontainers, blogging]
image: /assets/images/terminal.jpg
---

![Coding Setup in Terminal](/assets/images/terminal.jpg)
*A modern terminal setup for Jekyll development.*

This blog is built with **Jekyll** and runs entirely inside a **dev container** — meaning the whole authoring and build environment is version-controlled and portable. Here's a quick look at the setup.

## Adding images

Using images in Jekyll is simple. Just drop them into `assets/images/` and reference them with standard Markdown:

```markdown
![Alt text](/assets/images/your-image.jpg)
```

You can even add captions by placing italicized text right below the image.

## Why a dev container?

Dev containers give you a reproducible environment without polluting your host machine. Everything — Ruby, Jekyll, Bundler — lives inside the container. Clone the repo, open in VS Code, and you're ready to write.

## Project structure

```
_config.yml        # Site-wide settings
_posts/            # Blog posts (Markdown)
about.markdown     # About page
index.markdown     # Home page
Gemfile            # Ruby dependencies
```

## Writing a new post

Add a Markdown file to `_posts/` following the naming convention:

```
YYYY-MM-DD-your-post-title.markdown
```

Each post starts with **front matter**:

```yaml
---
layout: post
title: "Your Post Title"
date: 2026-02-28
categories: topic
tags: [one, two]
---
```

## Building & previewing

Inside the dev container terminal:

```bash
bundle exec jekyll serve --host 0.0.0.0
```

Then open `http://localhost:4000` to preview the site. Jekyll watches for changes and rebuilds automatically.

## Code highlighting

Jekyll uses **Rouge** for syntax highlighting out of the box. Just use fenced code blocks:

```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

print(greet("world"))
```

```javascript
const greet = (name) => `Hello, ${name}!`;
console.log(greet("world"));
```

## What's next

Future posts will cover topics like static-site deployment, developer tooling, and practical configuration tips. Stay tuned.
