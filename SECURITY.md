# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in this project, please report it by:

1. **DO NOT** open a public issue
2. Email the maintainer or create a private security advisory on GitHub
3. Include detailed information about the vulnerability:
   - Description of the issue
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if available)

## Supported Versions

This project is a personal blog using Hugo and the PaperMod theme. We keep dependencies up to date with:

- Latest stable Hugo version
- Latest PaperMod theme (via git submodule)

## Security Best Practices

This project follows these security practices:

- No sensitive data or credentials in the repository
- Build artifacts excluded from version control
- Dependencies managed via git submodules
- Automated deployment via GitHub Actions
- Static site generation (no server-side code execution)

## Known Security Considerations

As a static site hosted on GitHub Pages:
- No database or backend servers
- No user authentication or data collection
- No server-side code execution
- Content is public by design

## Updates

Security updates to Hugo or the PaperMod theme should be applied by:

```bash
# Update Hugo to latest version (system-dependent)
# For macOS with Homebrew:
brew upgrade hugo

# Update PaperMod theme
git submodule update --remote --merge
```

Thank you for helping keep this project secure!
