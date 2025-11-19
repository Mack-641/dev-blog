# Contributing to Dev Blog

Thank you for your interest in contributing! This document provides guidelines for contributing to this blog.

## How to Contribute

### Reporting Issues

If you find a typo, technical error, or broken link:

1. Check if the issue already exists in [GitHub Issues](https://github.com/Mack-641/dev-blog/issues)
2. If not, create a new issue with a clear description
3. Include the page URL and describe the problem

### Suggesting Content

Have an idea for a new post or topic?

1. Open an issue with the tag "content suggestion"
2. Describe the topic and why it would be valuable
3. Wait for feedback before starting to write

### Submitting Changes

1. **Fork the repository**
   ```bash
   git clone https://github.com/YOUR-USERNAME/dev-blog.git
   cd dev-blog
   git submodule update --init --recursive
   ```

2. **Create a branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make your changes**
   - Follow the existing style and formatting
   - Test locally with `hugo server -D`
   - Ensure markdown is properly formatted
   - Add appropriate tags to posts

4. **Commit your changes**
   ```bash
   git add .
   git commit -m "Brief description of changes"
   ```

5. **Push and create a Pull Request**
   ```bash
   git push origin feature/your-feature-name
   ```
   Then open a PR on GitHub with a clear description of changes

## Writing Guidelines

### Content Style

- **Be concise**: Focus on practical, actionable content
- **Use examples**: Include code snippets and real-world examples
- **Be accurate**: Verify commands and code work before publishing
- **Add context**: Explain why, not just how
- **Use proper formatting**: Follow markdown best practices

### Post Structure

```markdown
---
title: "Your Post Title"
date: 2025-10-17T17:11:08+02:00
draft: false
tags: ["tag1", "tag2"]
---

Brief introduction (1-2 sentences).

<!--more-->

## Section 1

Content...

## Section 2

Content...
```

### Code Snippets

Always specify the language for syntax highlighting:

````markdown
```bash
echo "Hello, World!"
```

```python
def hello():
    print("Hello, World!")
```
````

### Images

Place images in `assets/images/` and reference them:

```markdown
![Alt text](/images/filename.png)
```

## Development Setup

### Prerequisites

- Hugo Extended (latest version)
- Git

### Local Testing

```bash
# Start development server
hugo server -D

# Build site
hugo --minify

# Update theme
git submodule update --remote --merge
```

## Code Review Process

1. All submissions require review
2. Maintainers will review PRs within a few days
3. Address any requested changes
4. Once approved, changes will be merged

## Questions?

Feel free to open an issue for any questions about contributing!

## Code of Conduct

Be respectful and constructive in all interactions. We aim to create a welcoming environment for everyone.

---

Thank you for contributing to Dev Blog! 🎉
