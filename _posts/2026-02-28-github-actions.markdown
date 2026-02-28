---
layout: post
title: "CI/CD with GitHub Actions"
date: 2026-02-28 19:30:15 +0000
categories: devops cicd
tags: ["github-actions","ci-cd","devops","cheatsheet"]
image: /assets/images/actions.jpg

render_with_liquid: false
---


A practical reference for writing GitHub Actions workflows — from basic CI to full deployment pipelines.


## Core concepts

| Term | Description |
|---|---|
| **Workflow** | YAML file in `.github/workflows/` — defines automation |
| **Event** | Trigger that starts a workflow (push, PR, schedule, etc.) |
| **Job** | Set of steps that run on the same runner |
| **Step** | Individual task — a shell command or an `uses` action |
| **Runner** | The VM that executes a job (`ubuntu-latest`, `windows-latest`, etc.) |
| **Action** | Reusable unit of work from the Marketplace or your own repo |
| **Context** | Objects with runtime data (`github`, `env`, `secrets`, `steps`, etc.) |

---

## Workflow file structure

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: npm test
```

---

## Triggers (on:)

```yaml
on:
  # Push to specific branches/paths
  push:
    branches: [main, "release/**"]
    paths: ["src/**", "package.json"]
    paths-ignore: ["**.md"]

  # Pull requests
  pull_request:
    branches: [main]
    types: [opened, synchronize, reopened, ready_for_review]

  # Manual trigger (with inputs)
  workflow_dispatch:
    inputs:
      environment:
        description: "Target environment"
        required: true
        type: choice
        options: [staging, production]
        default: staging
      debug:
        type: boolean
        default: false

  # Scheduled (cron)
  schedule:
    - cron: "0 2 * * 1-5"   # 2 AM weekdays

  # After another workflow completes
  workflow_run:
    workflows: ["CI"]
    types: [completed]

  # When a release is published
  release:
    types: [published]
```

---

## Jobs & steps

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 15        # fail if job exceeds this

    steps:
      # Checkout code
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0       # full history (needed for git log, changelogs)

      # Setup a language runtime
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"

      # Run commands
      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm test -- --coverage

      # Upload artifacts
      - uses: actions/upload-artifact@v4
        if: always()           # upload even if tests fail
        with:
          name: coverage-report
          path: coverage/
          retention-days: 7

      # Download artifacts from another job
      - uses: actions/download-artifact@v4
        with:
          name: build-output
          path: dist/
```

---

## Matrix builds

Test across multiple versions/platforms in parallel:

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false        # don't cancel other jobs on first failure
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node: ["18", "20", "22"]
        exclude:
          - os: windows-latest
            node: "18"        # skip this specific combination
        include:
          - os: ubuntu-latest
            node: "20"
            experimental: true  # add extra variable to one combination

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      - run: npm ci && npm test
```

---

## Environment variables & secrets

```yaml
env:
  NODE_ENV: production        # workflow-level (all jobs)

jobs:
  deploy:
    runs-on: ubuntu-latest
    env:
      API_URL: https://api.example.com  # job-level

    steps:
      - name: Deploy
        env:
          API_KEY: ${{ secrets.API_KEY }}            # from repo/org secrets
          DB_URL: ${{ vars.DATABASE_URL }}           # from variables (non-secret)
          SHA: ${{ github.sha }}                     # built-in context
        run: |
          echo "Deploying $SHA to $API_URL"
          ./deploy.sh
```

Set environment variables dynamically between steps:

```yaml
- name: Get version
  id: version
  run: echo "tag=$(git describe --tags --abbrev=0)" >> $GITHUB_OUTPUT

- name: Use version
  run: echo "Deploying ${{ steps.version.outputs.tag }}"
```

---

## Conditions & expressions

```yaml
steps:
  # Only run on main branch
  - name: Deploy
    if: github.ref == 'refs/heads/main'
    run: ./deploy.sh

  # Skip on draft PRs
  - name: Run tests
    if: github.event.pull_request.draft == false

  # Run even if previous steps fail
  - name: Notify on failure
    if: failure()
    run: ./notify-slack.sh

  # Run if any previous step failed
  - name: Cleanup
    if: always()
    run: ./cleanup.sh

  # Expression syntax
  - name: Conditional
    if: |
      contains(github.event.head_commit.message, '[skip ci]') == false &&
      github.actor != 'dependabot[bot]'
    run: echo "Running..."
```

---

## Caching dependencies

```yaml
# Cache npm dependencies
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-

# setup-node has built-in caching (preferred for npm/yarn/pnpm)
- uses: actions/setup-node@v4
  with:
    node-version: "20"
    cache: "npm"               # or "yarn" or "pnpm"

# Cache Python packages
- uses: actions/setup-python@v5
  with:
    python-version: "3.12"
    cache: "pip"

# Cache Docker layers
- uses: actions/cache@v4
  with:
    path: /tmp/.buildx-cache
    key: ${{ runner.os }}-buildx-${{ github.sha }}
    restore-keys: |
      ${{ runner.os }}-buildx-
```

---

## Docker build & push

```yaml
jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: docker/setup-buildx-action@v3

      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - uses: docker/metadata-action@v5
        id: meta
        with:
          images: ghcr.io/${{ github.repository }}
          tags: |
            type=sha
            type=ref,event=branch
            type=semver,pattern={{version}}

      - uses: docker/build-push-action@v5
        with:
          context: .
          push: ${{ github.event_name != 'pull_request' }}
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

---

## Deployment environments

```yaml
jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    environment:
      name: staging
      url: https://staging.example.com
    steps:
      - run: ./deploy.sh staging

  deploy-production:
    runs-on: ubuntu-latest
    needs: deploy-staging       # run only after staging succeeds
    environment:
      name: production          # requires manual approval if configured
      url: https://example.com
    if: github.ref == 'refs/heads/main'
    steps:
      - run: ./deploy.sh production
```

Environments can require **manual reviewers** and **wait timers** — configure in repo Settings → Environments.

---

## Reusable workflows

Define once, call from multiple workflows:

```yaml
# .github/workflows/reusable-test.yml
on:
  workflow_call:
    inputs:
      node-version:
        type: string
        default: "20"
    secrets:
      NPM_TOKEN:
        required: false

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}
      - run: npm ci && npm test
```

Call it from another workflow:

```yaml
# .github/workflows/ci.yml
jobs:
  run-tests:
    uses: ./.github/workflows/reusable-test.yml
    with:
      node-version: "20"
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## Composite actions

Package a sequence of steps as a reusable action in your repo:

```yaml
# .github/actions/setup-app/action.yml
name: "Setup App"
description: "Checkout and install dependencies"
inputs:
  node-version:
    default: "20"
runs:
  using: composite
  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: ${{ inputs.node-version }}
        cache: "npm"
    - run: npm ci
      shell: bash
```

Use it:

```yaml
- uses: ./.github/actions/setup-app
  with:
    node-version: "20"
```

---

## Security best practices

```yaml
# Pin actions to a commit SHA (not a mutable tag)
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683  # v4.2.2

# Minimal permissions (principle of least privilege)
permissions:
  contents: read
  packages: write
  id-token: write             # only if using OIDC

# Avoid printing secrets
- run: |
    echo "::add-mask::${{ secrets.API_KEY }}"  # mask in logs
    curl -H "Authorization: Bearer $API_KEY" https://api.example.com
  env:
    API_KEY: ${{ secrets.API_KEY }}

# Prevent script injection from untrusted inputs (e.g. PR title)
- name: Safe use of PR title
  env:
    PR_TITLE: ${{ github.event.pull_request.title }}
  run: echo "PR: $PR_TITLE"   # use env var, never inline ${{ }} in run
```

---

## Common complete workflows

**Node.js CI:**

```yaml
name: CI
on: [push, pull_request]

jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "20", cache: "npm" }
      - run: npm ci
      - run: npm run lint
      - run: npm run build
      - run: npm test -- --coverage
      - uses: actions/upload-artifact@v4
        with: { name: dist, path: dist/ }
```

**Auto-release on tag:**

```yaml
name: Release
on:
  push:
    tags: ["v*"]

permissions:
  contents: write

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "20", cache: "npm" }
      - run: npm ci && npm run build
      - uses: softprops/action-gh-release@v1
        with:
          files: dist/**
          generate_release_notes: true
```

**Nightly dependency updates check:**

```yaml
name: Nightly
on:
  schedule:
    - cron: "0 6 * * 1"   # Monday 6 AM

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm audit --audit-level=high
```

---

## Useful built-in actions

| Action | Purpose |
|---|---|
| `actions/checkout` | Clone the repository |
| `actions/setup-node` | Install Node.js + cache |
| `actions/setup-python` | Install Python + cache |
| `actions/cache` | General caching |
| `actions/upload-artifact` | Save files between jobs |
| `actions/download-artifact` | Retrieve saved files |
| `actions/github-script` | Run JS with GitHub API |
| `docker/build-push-action` | Build and push Docker images |
| `softprops/action-gh-release` | Create GitHub releases |

---

## Debugging tips

```yaml
# Enable step debug logging
# Set secret ACTIONS_STEP_DEBUG = true in repo settings

# Print context for inspection
- name: Dump context
  run: |
    echo "Event: ${{ github.event_name }}"
    echo "Ref: ${{ github.ref }}"
    echo "SHA: ${{ github.sha }}"
    echo "Actor: ${{ github.actor }}"

# Tmate SSH session for interactive debugging (never in production)
- uses: mxschmitt/action-tmate@v3
  if: failure()
```

---

## Quick tips

- Use `actions/checkout@v4` with `fetch-depth: 0` when you need full git history.
- Cache aggressively — it's the single biggest speed-up for most workflows.
- Use `needs:` to chain jobs and `if: failure()` for cleanup/notification steps.
- Store non-sensitive config in **Variables** (Settings → Secrets and Variables → Variables).
- Pin third-party actions to a commit SHA to prevent supply chain attacks.
- Use `workflow_dispatch` inputs to make workflows manually runnable with parameters.
- Use `concurrency:` groups to cancel in-progress runs when a new one starts:

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

That's it — keep this as your GitHub Actions reference and extend with your own reusable workflow library.
