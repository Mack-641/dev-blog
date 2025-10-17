---
date: '2025-10-17T17:11:08+02:00'
draft: false
title: 'Markdown'
tags: ["Markdown"]
---

## How to use Markdown on this blog

This post is a short, practical guide to writing Markdown for this Hugo + PaperMod blog. Use it as a quick reference.

<!--more-->

## Basic text

- Headings: use `#` through `######`
  - Example: `## Heading level 2`
- Emphasis: `*italic*` or `**bold**`
- Inline code: surround with backticks: `` `inline()` ``
- Paragraphs: separate with a blank line

## Lists

Unordered:

- Item one
- Item two

Ordered:

1. First
2. Second

Nesting:

- Parent
  - Child

## Links and images

Link: `[Hugo](https://gohugo.io/)`  
Image (Markdown): `![alt text](/images/photo.jpg "Title")`  
PaperMod / Hugo shortcode image (preferred for resizing & captions):

```html
{{< figure src="/images/photo.jpg" title="Caption" >}}
```

## Code blocks and syntax highlighting

Use fenced code blocks with the language for syntax highlighting:

```go
// example.go
package main

func main() {
    println("Hello, Hugo")
}
```

## Blockquotes and horizontal rules

> This is a blockquote.

Horizontal rule:

---

## Tables

|  Feature | Supported |
| -------: | :-------: |
| Headings |    Yes    |
|     Code |    Yes    |

## Hugo-specific tips

- Front matter controls title, date, draft, tags, slug, and more.
- Use the summary separator `<!--more-->` to set the teaser shown on listing pages.
- Use shortcodes for richer content (figures, videos, embeds).
- For custom attributes (IDs/classes) enable and use Goldmark extensions in config if needed.

Example front matter fields commonly used here:

```yaml
---
title: "Post title"
date: 2025-10-17T17:11:08+02:00
draft: false
tags: ["Markdown","Tips"]
---
```

## PaperMod-specific notes

- Add `hero` or `image` in front matter if you want a header image (check theme docs).
- PaperMod shows reading time and tags automatically when front matter is set correctly.

## Good practices

- Keep posts small and focused.
- Use fenced code blocks with language names for consistent syntax highlighting.
- Add alt text for accessibility on images.
- Preview locally with `hugo server` before publishing:
  - Open a terminal in the project root and run: `hugo server -D`
  - Visit <http://localhost:1313>

## Quick checklist before publish

- [ ] Title and date set
- [ ] `draft: false`
- [ ] Tags added
- [ ] Summary (use `<!--more-->`) validated
- [ ] Images have alt text

That's it — use this as your cheat-sheet when writing posts in this repository.
