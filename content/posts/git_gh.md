+++
date = '2021-09-03T15:49:43+05:00'
title = ''
tags = ['git', 'github', 'cli']
+++

---
date: "2025-01-23T14:49:19+05:00"
title: "Mastering GitHub CLI: The Power of GitHub from Your Terminal"
---

## Introduction

GitHub has revolutionized collaboration in the software development world, and while Git remains the backbone of version control, managing repositories, issues, pull requests, and workflows often requires a visit to the GitHub web interface. Enter **GitHub CLI (`gh`)**—a powerful command-line tool designed to seamlessly integrate GitHub’s features directly into your terminal.

With `gh`, you can authenticate, manage repositories, handle issues and pull requests, and even trigger GitHub Actions without ever leaving your command line. This guide will walk you through its capabilities and how to leverage it for a streamlined development workflow.

---

## Why Use GitHub CLI?

The `gh` tool is an official GitHub project aimed at making interactions with GitHub more efficient and developer-friendly. It enhances Git’s core capabilities by allowing users to:

- Authenticate securely without manually managing tokens.
- Create, clone, and manage repositories with simple commands.
- Open, review, and merge pull requests directly from the terminal.
- Work with GitHub Issues, discussions, and projects effortlessly.
- Trigger and monitor GitHub Actions workflows without navigating the web interface.

If you’re a developer who frequently uses GitHub, `gh` can significantly boost productivity by reducing context switching between the browser and the terminal.

---

## Installation on Manjaro Linux

For Manjaro (and other Arch-based distributions), installing GitHub CLI is straightforward:

```sh
yay -S github-cli
```

Once installed, verify the installation:

```sh
gh --version
```

### Authenticating with GitHub CLI

To log in to GitHub using `gh`, run:

```sh
gh auth login
```

Follow the interactive prompts:

- Select **GitHub.com** or **GitHub Enterprise**.
- Choose authentication via **HTTPS** or **SSH**.
- Authenticate using a one-time browser-based login or SSH key.

To confirm authentication:

```sh
gh auth status
```

---

## Essential GitHub CLI Commands

### Repository Management

Create a new repository:
```sh
gh repo create my-repo --public
```

Clone an existing repository:
```sh
gh repo clone username/repository
```

Fork a repository:
```sh
gh repo fork username/repository
```

### Working with Issues & Pull Requests

List open issues:
```sh
gh issue list
```

Create a new issue:
```sh
gh issue create --title "Bug Report" --body "Describe the bug"
```

View and merge pull requests:
```sh
gh pr list
```
```sh
gh pr merge 123 --squash --delete-branch
```

### Managing GitHub Actions

List workflow runs:
```sh
gh run list
```

Trigger a workflow manually:
```sh
gh workflow run workflow-name.yml
```

View detailed logs of a failed run:
```sh
gh run view --log
```

---

## Using Git and GitHub CLI Together

While `gh` is great for managing GitHub-specific tasks, you’ll still need Git for version control. Here’s how they complement each other:

### Install Git (if not already installed):
```sh
sudo pacman -S git
```

### Set up Git and GitHub CLI together:
```sh
git clone https://github.com/username/repository.git
cd repository
git add .
git commit -m "Initial commit"
git push origin master
```

Now, with `gh`, you can create a pull request for your changes without opening the browser:
```sh
gh pr create --title "New Feature" --body "Added a new feature to enhance performance"
```

---

## Conclusion

GitHub CLI is a game-changer for developers who rely on GitHub for collaboration. It bridges the gap between Git and GitHub’s web interface, making workflows more efficient and command-driven. Whether you’re managing repositories, collaborating on issues, or triggering workflows, `gh` empowers you to do it all from the terminal.

If you haven't yet explored `gh`, now is the time to integrate it into your development process and take your GitHub workflow to the next level!

