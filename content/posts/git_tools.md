+++
date = '2021-09-04T15:49:43+05:00'
title = 'Optimizing GitHub Workflow: Mastering GitHub CLI and Personal Access Tokens'
tags = ['gh', 'github', 'cli', 'pat', 'tokens']
+++

## Introduction

GitHub has revolutionized software development collaboration. While Git remains the core tool for version control, GitHub CLI (`gh`) and Personal Access Tokens (PATs) provide powerful ways to authenticate and manage GitHub operations directly from the terminal. 

This guide covers everything from generating a secure PAT to leveraging GitHub CLI for seamless repository, issue, and workflow management.

---

## Creating a Personal Access Token (PAT)

A Personal Access Token (PAT) acts as a secure alternative to passwords for authentication when interacting with GitHub via the command line or API.

### Steps to Generate a PAT

1. Navigate to the upper-right corner of your GitHub page and click on your profile photo. From the dropdown menu, select **Settings**.
2. In the left sidebar, click **Developer settings**.
3. Select **Personal access tokens**.
4. Click **Generate new token**.
5. Provide a descriptive name for your token to easily identify its purpose.
6. Set an expiration date using the **Expiration** dropdown menu.
7. Assign the necessary scopes (e.g., select **repo** for repository access).
8. Click **Generate token**.
9. Securely store the generated token as it will not be visible again.

### Important Considerations
- Treat your PAT like a password—store it securely and never share it publicly.
- Regularly review and regenerate tokens as needed to maintain security.

---

## Why Use GitHub CLI?

The `gh` tool simplifies interactions with GitHub, making it a must-have for developers who want to manage repositories, issues, pull requests, and workflows directly from the terminal.

### Key Benefits:
- **Secure authentication** without handling passwords or tokens manually.
- **Efficient repository management** (create, clone, fork, and manage repos effortlessly).
- **Seamless collaboration** (list, create, and merge pull requests and issues).
- **GitHub Actions control** (trigger and monitor workflows without a web browser).

---

## Installing GitHub CLI on Manjaro Linux

For Manjaro (and other Arch-based distributions), install `gh` using:

```sh
yay -S github-cli
```

Verify the installation:

```sh
gh --version
```

### Authenticating with GitHub CLI

Login using:

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

### Managing Issues & Pull Requests

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

### Controlling GitHub Actions

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

## Using Git, GitHub CLI, and PAT Together

While `gh` is excellent for managing GitHub tasks, Git remains the foundation for version control. Here’s how they work together:

### Install Git (if not already installed):
```sh
sudo pacman -S git
```

### Set up Git and GitHub CLI with PAT authentication:

```sh
git clone https://github.com/username/repository.git
cd repository
git add .
git commit -m "Initial commit"
git push origin main
```

If using HTTPS with PAT authentication:
```sh
git remote set-url origin https://github.com/username/repository.git
```

Now, create a pull request using GitHub CLI:
```sh
gh pr create --title "New Feature" --body "Added a new feature to enhance performance"
```

---

## Conclusion

By combining Personal Access Tokens with GitHub CLI, developers gain an efficient, secure, and command-driven approach to managing repositories, collaborating on code, and automating workflows. Whether you’re authenticating via PAT, creating pull requests, or triggering GitHub Actions, this guide provides the essential tools to optimize your GitHub workflow.

