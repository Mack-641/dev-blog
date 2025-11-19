# Dev Blog

A developer blog built with [Hugo](https://gohugo.io/) and the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme. This blog contains technical notes, tutorials, and cheat sheets about web development, DevOps, and various tools.

[![Deploy Hugo site to GitHub Pages](https://github.com/Mack-641/dev-blog/actions/workflows/gh-pages.yml/badge.svg)](https://github.com/Mack-641/dev-blog/actions/workflows/gh-pages.yml)

## 🌐 Live Site

Visit the blog at: [https://mack-641.github.io/dev-blog/](https://mack-641.github.io/dev-blog/)

## 📚 What's Inside

This blog includes practical guides and cheat sheets on:

- **Git** - Version control commands and workflows
- **Docker** - Container management and best practices  
- **Linux** - Essential commands and system administration
- **PostgreSQL** - Database management and queries
- **SOLID Principles** - Object-oriented design patterns
- **Markdown** - Writing and formatting for Hugo

## 🚀 Quick Start

### Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) (latest version recommended)
- [Git](https://git-scm.com/)

### Local Development

1. **Clone the repository with submodules** (for the PaperMod theme):

   ```bash
   git clone --recurse-submodules https://github.com/Mack-641/dev-blog.git
   cd dev-blog
   ```

   If you already cloned without submodules, run:
   
   ```bash
   git submodule update --init --recursive
   ```

2. **Start the Hugo development server**:

   ```bash
   hugo server -D
   ```

   The site will be available at `http://localhost:1313/dev-blog/`

3. **Build the site** (optional):

   ```bash
   hugo --minify
   ```

   The static site will be generated in the `public/` directory.

## 📝 Creating New Posts

Use Hugo's archetype system to create new posts:

```bash
hugo new posts/my-new-post.md
```

This creates a new post in `content/posts/` with the default frontmatter. Edit the file and set `draft: false` when ready to publish.

### Post Frontmatter Example

```yaml
---
title: "My Awesome Post"
date: 2025-10-17T17:11:08+02:00
draft: false
tags: ["tag1", "tag2"]
---

Post content goes here...
```

## 🎨 Customization

### Configuration

The main configuration file is `hugo.yaml`. Key settings:

- **Site metadata**: title, baseURL, language
- **Theme parameters**: reading time, share buttons, TOC
- **Menu items**: customize navigation
- **Social links**: add your GitHub, Twitter, etc.

### Theme

This site uses the [PaperMod theme](https://github.com/adityatelange/hugo-PaperMod) as a Git submodule.

To update the theme:

```bash
git submodule update --remote --merge
```

## 🔄 Deployment

The site automatically deploys to GitHub Pages using GitHub Actions when changes are pushed to the `master` branch.

The workflow (`.github/workflows/gh-pages.yml`):
1. Checks out the repository with submodules
2. Sets up Hugo
3. Builds the site with `hugo --minify`
4. Deploys to the `gh-pages` branch

## 📂 Project Structure

```
dev-blog/
├── archetypes/          # Post templates
├── assets/              # Images and custom assets
│   └── images/          # Site images
├── content/             # Markdown content
│   ├── about.md         # About page
│   ├── posts/           # Blog posts
│   └── search.md        # Search page
├── public/              # Generated site (gitignored)
├── resources/           # Hugo generated resources
├── themes/              # Hugo themes
│   └── PaperMod/        # PaperMod theme (submodule)
├── .github/             
│   └── workflows/       # GitHub Actions workflows
├── hugo.yaml            # Hugo configuration
└── README.md            # This file
```

## 🤝 Contributing

Contributions are welcome! If you find an error or want to improve the content:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Make your changes
4. Commit your changes (`git commit -am 'Add some improvement'`)
5. Push to the branch (`git push origin feature/improvement`)
6. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Hugo](https://gohugo.io/) - The world's fastest framework for building websites
- [PaperMod Theme](https://github.com/adityatelange/hugo-PaperMod) - A fast, clean, and responsive Hugo theme
- All contributors and readers of this blog

## 📧 Contact

- GitHub: [@Mack-641](https://github.com/Mack-641)
- Blog: [https://mack-641.github.io/dev-blog/](https://mack-641.github.io/dev-blog/)

---

Made with ❤️ using Hugo and PaperMod
