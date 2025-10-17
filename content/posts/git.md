---
title: "Git Command Cheatsheet"
date: '2025-10-17T17:11:08+02:00'
draft: false
tags: ["git","cheatsheet","workflow"]
---

A compact, practical Git command reference for daily use — copy commands and adapt them to your repo. Use this as a quick cheat-sheet.

<!--more-->

## Setup & identity

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global core.editor "code --wait"
```

## Create & clone

```bash
git init                 # create a repo
git clone <url>          # clone remote repo
```

## Basic workflow

```bash
git status               # show changes
git add <file>           # stage a file
git add .                # stage all changes
git commit -m "message"  # commit staged changes
git push origin main     # push to remote branch
git pull --rebase        # update local branch with remote changes (rebase)
```

## Branching

```bash
git branch               # list local branches
git branch <name>        # create branch
git checkout <name>      # switch branch
git switch -c <name>     # create + switch (modern)
git merge <branch>       # merge branch into current
git rebase <branch>      # rebase current onto branch
```

## Inspecting history & diffs

```bash
git log --oneline --graph --decorate --all
git log -p <file>        # show commits & diffs for file
git diff                 # unstaged changes
git diff --staged        # staged vs HEAD
```

## Undo & fix mistakes

```bash
git restore <file>               # discard working-dir changes (modern)
git restore --staged <file>      # unstage file
git checkout -- <file>           # older form to discard
git reset --soft HEAD~1          # undo last commit, keep changes staged
git reset --mixed HEAD~1         # undo last commit, keep changes unstaged
git revert <commit>              # create a commit that undoes another commit
```

## Working with remotes

```bash
git remote -v
git remote add origin <url>
git fetch origin
git pull origin main
git push -u origin feature/xyz  # push and set upstream
git push --delete origin <branch> # delete remote branch
```

## Stashing

```bash
git stash               # save working state
git stash pop           # restore and remove stash
git stash list
git stash apply stash@{1}  # apply specific stash
```

## Cherry-pick & selective changes

```bash
git cherry-pick <commit>       # apply a commit from another branch
git add -p                     # interactively stage hunks
```

## Useful aliases (add to ~/.gitconfig)

```ini
[alias]
  st = status
  co = checkout
  br = branch
  lg = log --oneline --graph --decorate --all
  amend = commit --amend --no-edit
```

## Tips

- Use `git status` often.
- Prefer feature branches: `feature/*` or `fix/*`.
- Use `git pull --rebase` to keep history linear when collaborating.
- Use `git log --graph --oneline` to visualize history.
- Back up important work before using destructive commands (`reset --hard`, etc.).

That's it — save this post for quick reference and extend it with commands you use most.