# Repository Setup Guide

This document contains recommended settings for the GitHub repository to make it more discoverable and professional.

## Repository Description

Add this description to your GitHub repository:

```
A developer blog built with Hugo and PaperMod theme, featuring technical guides, cheat sheets, and tutorials on Git, Docker, Linux, PostgreSQL, and more.
```

## Repository Topics

Add these topics to your repository to improve discoverability:

- `hugo`
- `blog`
- `developer-blog`
- `hugo-theme`
- `papermod`
- `documentation`
- `cheatsheet`
- `tutorial`
- `devops`
- `web-development`
- `static-site`
- `github-pages`

### How to Add Topics

1. Go to your repository on GitHub
2. Click on the gear icon ⚙️ next to "About"
3. Add the topics listed above in the "Topics" field
4. Add the repository description
5. Set the website URL to: `https://mack-641.github.io/dev-blog/`
6. Click "Save changes"

## Repository Settings

### General Settings

- ✅ Set repository description and website
- ✅ Add relevant topics
- ✅ Enable "Packages" if you plan to use them
- ✅ Disable "Wikis" if not used
- ✅ Disable "Projects" if not used
- ✅ Enable "Preserve this repository" (optional, for important projects)

### Pages Settings

Verify GitHub Pages is configured:

1. Go to Settings → Pages
2. Source should be: "Deploy from a branch"
3. Branch: `gh-pages` / `/ (root)`
4. Verify the site is published at: `https://mack-641.github.io/dev-blog/`

### Branch Protection (Optional)

For the `master` branch:

1. Go to Settings → Branches
2. Add branch protection rule for `master`
3. Consider enabling:
   - Require pull request reviews before merging
   - Require status checks to pass
   - Require conversation resolution before merging

### Community Health Files

The following files are now present in the repository:

- ✅ README.md
- ✅ LICENSE
- ✅ CONTRIBUTING.md
- ✅ CODE_OF_CONDUCT.md
- ✅ SECURITY.md
- ✅ .gitignore

### Repository Insights

After setting up, verify the "Community" tab shows:

- ✅ Description
- ✅ README
- ✅ Code of conduct
- ✅ Contributing guide
- ✅ License
- ✅ Security policy

## Social Preview

Consider creating a social preview image:

1. Go to Settings → General
2. Scroll to "Social preview"
3. Upload an image (1280×640px recommended)
4. This image appears when the repository is shared on social media

You can use one of the existing images from `assets/images/` or create a custom one.

## GitHub Achievements

To maximize GitHub achievements:

1. **Quickdraw** - Open a pull request within 5 minutes of creating an issue
2. **Pair Extraordinaire** - Co-author a commit
3. **Pull Shark** - Open pull requests that get merged
4. **Galaxy Brain** - Get answers marked as accepted on GitHub Discussions
5. **YOLO** - Merge a pull request without review (not recommended!)

This repository is now well-structured for collaboration and discovery!
