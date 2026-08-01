---
title: "GitHub CLI and Personal Access Tokens: What I Use Daily"
author: "Saad"
date: 2021-09-04
tags: ["gh", "github", "cli", "pat", "tokens"]
categories: ["DevOps"]
---

## Why I Bother With This

I got tired of opening the browser every time I wanted to create a repo, check a PR, or trigger a workflow. **GitHub CLI** (`gh`) lets me do most of that from the terminal. **Personal Access Tokens (PATs)** are how Git authenticates when you push over HTTPS.

This post covers both — how I generate a PAT and how I use `gh` day to day.

---

## Creating a Personal Access Token (PAT)

A PAT is basically a password for the command line.

1. GitHub → profile photo → **Settings**
2. **Developer settings** → **Personal access tokens**
3. **Generate new token**
4. Give it a name you'll recognize later
5. Set an expiration date
6. Check **repo** (and any other scopes you need)
7. **Generate token** — copy it now, you won't see it again

Treat it like a password. Don't commit it to a repo or paste it in chat.

---

## Install GitHub CLI

**Arch Linux:**

```sh
sudo pacman -S github-cli
```

Or via AUR:

```sh
yay -S github-cli
```

**Ubuntu:**

```sh
sudo apt update
sudo apt install gh
```

Check:

```sh
gh --version
```

## Log In

```sh
gh auth login
```

Follow the prompts — I usually pick GitHub.com, HTTPS, and browser login.

Confirm:

```sh
gh auth status
```

---

## Commands I Actually Use

### Repos

```sh
gh repo create my-repo --public
gh repo clone username/repository
gh repo fork username/repository
```

### Issues and PRs

```sh
gh issue list
gh issue create --title "Bug Report" --body "What went wrong"
gh pr list
gh pr merge 123 --squash --delete-branch
```

### GitHub Actions

```sh
gh run list
gh workflow run workflow-name.yml
gh run view --log
```

---

## Git + gh Together

I still use plain Git for commits and pushes. `gh` handles the GitHub-specific stuff.

```sh
git clone https://github.com/username/repository.git
cd repository
git add .
git commit -m "Initial commit"
git push origin main
```

Then open a PR without leaving the terminal:

```sh
gh pr create --title "New feature" --body "What this changes"
```

---

## Bottom Line

PAT for auth, `gh` for GitHub tasks, Git for everything else. That's my setup and it saves me a lot of clicking.
