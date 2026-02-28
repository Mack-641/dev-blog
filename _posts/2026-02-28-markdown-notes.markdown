---
layout: post
title: "Markdown"
date: 2026-02-28 19:30:15 +0000
categories: writing markdown
tags: ["markdown"]
image: /assets/images/coding.jpg
render_with_liquid: false
---

## How to use Markdown on this blog

A short, practical guide to writing Markdown for this Jekyll blog. Use it as a quick reference.


## Basic text

```markdown
# Heading level 1
## Heading level 2
### Heading level 3

*italic*   **bold**   ***bold italic***   ~~strikethrough~~

`inline code`

Paragraphs are separated by a blank line.

Line break without a new paragraph:  
add two spaces at the end of a line.
```

---

## Lists

Unordered:

```markdown
- Item one
- Item two
  - Nested item (two spaces indent)
```

Ordered:

```markdown
1. First
2. Second
3. Third
```

Task list (GitHub-flavored Markdown via Kramdown):

```markdown
- [x] Done
- [ ] Not done
- [ ] Pending
```

---

## Links and images

```markdown
[link text](https://example.com/)
[link with title](https://example.com/ "Homepage")
[reference-style link][ref-id]

[ref-id]: https://example.com/ "Example"

![alt text](/assets/images/photo.jpg)
![alt text](/assets/images/photo.jpg "Optional title")
```

Store images in `assets/images/` and reference them with an absolute path from the site root.
Add a caption by placing italicised text immediately below the image:

```markdown
![alt text](/assets/images/photo.jpg)
*This becomes the caption.*
```

---

## Code blocks & syntax highlighting

Use fenced code blocks and specify the language for highlighting:

````markdown
```python
def greet(name: str) -> str:
    return f"Hello, {name}!"
```
````

Supported language identifiers include: `go`, `ts`, `js`, `python`, `bash`, `sql`, `yaml`, `json`, `html`, `css`, `dockerfile`, `markdown`, and many more.

Inline code: surround with backticks: `` `myFunction()` ``

To show a literal backtick inside inline code, use double backticks:
``` `` `backtick` `` ```

---

## Blockquotes

```markdown
> This is a blockquote.
>
> It can span multiple paragraphs.

> Nested:
> > Inner quote
```

---

## Tables

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|:--------:|---------:|
| left     | center   | right    |
| foo      | bar      | baz      |
```

Rendered:

| Column 1 | Column 2 | Column 3 |
|----------|:--------:|---------:|
| left     | center   | right    |
| foo      | bar      | baz      |

Tips: column widths don't need to match, pipes on the outside are optional but recommended for readability.

---

## Horizontal rule

Three or more hyphens, asterisks, or underscores on a line:

```markdown
---
```

---

## Footnotes (Kramdown)

```markdown
Here is a footnote reference.[^1]

[^1]: And here is the footnote definition.
```

---

## Escaping special characters

Backslash escapes Markdown syntax characters:

```markdown
\*not italic\*
\[not a link\]
\`not code\`
```

---

## Jekyll includes

Jekyll doesn't have Hugo-style shortcodes, but you can create reusable HTML snippets in `_includes/` and call them in Liquid-enabled pages:

```liquid
{% include some-snippet.html param="value" %}
```

For code highlighting with line numbers, use the built-in `highlight` tag:

```liquid
{% highlight python linenos %}
def greet(name):
    return f"Hello, {name}!"
{% endhighlight %}
```

---

## Front matter reference

```yaml
---
layout: post
title: "Post title"
date: 2026-02-28 19:30:15 +0000
categories: topic subtopic
tags: [tag1, tag2]
description: "Short description shown in meta and search results"
render_with_liquid: false   # set to true only if post contains {{ }} syntax
---
```

---

## Minima theme notes

- `jekyll-seo-tag` automatically generates Open Graph and Twitter Card meta tags from `title` and `description` in front matter.
- Post excerpts shown on the home page come from the first paragraph of each post. Override by adding `excerpt:` in front matter.
- `render_with_liquid: false` prevents Jekyll from misinterpreting `{{ }}` or `{% %}` syntax inside code examples.
- Syntax highlighting is provided by **Rouge** — use fenced code blocks with the language identifier.

---

## Useful Jekyll commands

```bash
bundle exec jekyll serve --host 0.0.0.0 --watch   # serve locally with auto-rebuild
bundle exec jekyll build                           # build into ./_site
bundle exec jekyll build JEKYLL_ENV=production     # production build
bundle-audit update && bundle-audit check          # security audit
```

Preview at <http://localhost:4000>.

---

## Good practices

- Keep posts small and focused.
- Use fenced code blocks with a language name for consistent syntax highlighting.
- Add alt text on all images for accessibility.
- Use `description:` in front matter to control the excerpt and SEO meta description.
- Use `lastmod:` to signal content updates to search engines and RSS readers.
- Store images in `assets/images/` and use absolute paths: `/assets/images/photo.jpg`.
- Preview locally before publishing: `bundle exec jekyll serve --host 0.0.0.0` → <http://localhost:4000>

---

## Quick checklist before publish

- [ ] Title and date set correctly in front matter
- [ ] `layout: post` present
- [ ] Tags and categories added
- [ ] Description set (improves SEO and excerpt)
- [ ] Images stored in `assets/images/` with alt text
- [ ] Internal links work locally
- [ ] `bundle exec jekyll build` shows no errors or warnings

That's it — use this as your cheat-sheet when writing posts in this repository.
