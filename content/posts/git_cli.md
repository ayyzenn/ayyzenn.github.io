+++
date = '2021-09-03T15:49:43+05:00'
title = 'Comprehensive Guide to GitHub: Getting Started and Uploading Files via Command Line'
tags = ['git', 'github', 'cli']
+++

# Introduction

This guide provides a comprehensive introduction to GitHub, covering:

- What is GitHub?
- Why use GitHub?
- How to use GitHub?
- Installing and configuring Git
- Uploading files to GitHub using the command line

## What is GitHub?

Before diving into GitHub, it is essential to understand Git and Open-Source Projects.

### 1. Git

Git is a distributed version control system that allows developers to track changes in their projects, revert to previous versions, and collaborate efficiently.

### 2. Open-Source Projects

Open-source projects are software projects where the source code is publicly available, allowing anyone to use, modify, and contribute to the development.

GitHub plays a crucial role by providing a web-based platform that hosts Git repositories, offering version control and collaboration tools.

Millions of developers use GitHub to share code, contribute to open-source projects, and manage software development workflows.

## Why Use GitHub?

GitHub provides a cloud-based repository system that allows developers to:

- Host and manage open-source projects.
- Track changes and maintain version control using Git.
- Collaborate with other developers by contributing to existing repositories.
- Enhance project visibility and engagement within the developer community.

With millions of developers contributing to open-source projects, GitHub has become one of the most influential platforms for software development.

## How to Use GitHub?

1. Search for GitHub in your browser and navigate to the official website.
2. Click on the first link in the search results to access the GitHub homepage.
3. Sign in by entering your username or email address and password, then click **Sign in**.
4. Once signed in, you will be directed to your GitHub dashboard.

From the dashboard, you can create repositories, upload files, and explore other repositories to collaborate with developers worldwide.

## Installing Git

To use GitHub effectively, you need to install Git on your system. Run the following command in your terminal:

```sh
sudo apt-get install git
```

After installation, verify it using:

```sh
git --version
```

## Configuring Git

Once Git is installed, configure your username and email address. This information is associated with every commit you make.

Set your global commit name and email using:

```sh
git config --global user.name "Your Name"
git config --global user.email "youremail@yourdomain.com"
```

To verify the configuration changes, run:

```sh
git config --list
```

The output should resemble:

```sh
user.name=Your Name
user.email=your_email@your_domain.com
```

## Cloning a Repository

Before uploading a file, clone the target GitHub repository using the following command:

```sh
git clone https://github.com/githubusername/repository
```

> **Note:** To get your GitHub username and repository name, navigate to your GitHub dashboard, select the repository, and copy the repository URL from the address bar.

## Copying and Adding Files

Once the repository is cloned, copy the file into the cloned repository directory:

```sh
cp filename clone_directory_name
```

Replace `filename` with the actual file name and its extension.

Next, add the file to the Git staging area:

```sh
git add .
```

## Committing Changes

Commit the changes with a descriptive message:

```sh
git commit -m "Uploading file as requested"
```

> **Note:** Git recommends including meaningful commit messages.

## Pushing the File to GitHub

Push the file to GitHub by running:

```sh
git push origin master
```

You'll be prompted to enter your GitHub username and [Personal Access Token (PAT)](/posts/git_tools).

Once this is done, your file is successfully uploaded to GitHub.

---

By following this guide, you will have a strong understanding of GitHub, its importance, and how to use it effectively through the command line.
