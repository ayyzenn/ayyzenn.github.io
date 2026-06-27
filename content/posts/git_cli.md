---
title: "GitHub from the Command Line: How I Push My Code"
date: 2021-09-03
tags: ["git", "github", "cli"]
---

## Why I Use GitHub

I use **Git** to track changes in my code and **GitHub** to host it online. Git runs on my machine. GitHub is the website that stores my repos and lets me share them.

If you're new: Git is the tool, GitHub is where the repos live.

## Install Git

**Ubuntu:**

```sh
sudo apt update
sudo apt install git
```

**Arch Linux:**

```sh
sudo pacman -S git
```

Check it worked:

```sh
git --version
```

## Set Your Name and Email

Git attaches this info to every commit:

```sh
git config --global user.name "Your Name"
git config --global user.email "youremail@yourdomain.com"
```

Verify:

```sh
git config --list
```

## Clone a Repo

Before adding files, clone the repo you want to work on:

```sh
git clone https://github.com/githubusername/repository
cd repository
```

Copy the URL from your repo page on GitHub.

## Add, Commit, Push

After you add or edit files:

```sh
git add .
git commit -m "Describe what you changed"
git push origin main
```

If your default branch is `master`, use that instead of `main`.

GitHub will ask for your username and a [Personal Access Token (PAT)](/posts/git_tools/) — not your account password.

## That's It

That's the workflow I use every time: clone, edit, add, commit, push. Nothing fancy, but it gets the job done.
