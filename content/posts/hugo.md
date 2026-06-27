---
title: "Hugo: The Static Site Generator I Use for This Blog"
date: 2020-12-03
tags: ["hugo", "github"]
---

## Why I Picked Hugo

I wanted a blog that loads fast, doesn't need a database, and lets me write posts in Markdown. **Hugo** does exactly that — it builds plain HTML files from my content, and I host them on GitHub Pages.

No WordPress, no PHP, no MySQL. Just markdown files and a `hugo` command.

## What I Like About It

- **Fast builds** — my whole site compiles in under a second
- **Markdown posts** — write in any editor, commit to Git
- **Themes** — I'm using PaperMod
- **GitHub Pages** — free hosting, deploys on push

## How I Set Up Hugo

### 1. Install Hugo

#### On Arch Linux

```bash
sudo pacman -S hugo
```

#### On Ubuntu

```bash
sudo apt update
sudo apt install hugo
```

Or install the extended edition from the official release page (recommended for themes that use Sass):

```bash
wget https://github.com/gohugoio/hugo/releases/download/v0.163.3/hugo_extended_0.163.3_linux-amd64.deb
sudo dpkg -i hugo_extended_0.163.3_linux-amd64.deb
```

#### On macOS (Using Homebrew)
```bash
brew install hugo
```

Alternatively, you can download the latest binary from [Hugo's official GitHub releases](https://github.com/gohugoio/hugo/releases).

### 2. Create a New Hugo Site

```bash
hugo new site my-blog
cd my-blog
```

This will generate a new Hugo project with the default folder structure.

### 3. Add a Theme

Hugo supports themes, which you can find at [Hugo Themes](https://themes.gohugo.io/). To install a theme, clone it into the `themes` directory:

```bash
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/hugo-PaperMod
```

Then, edit `hugo.toml` and set the theme:

```toml
theme = "hugo-PaperMod"
```

### 4. Create Content

Generate a new post using:

```bash
hugo new posts/my-first-post.md
```

Edit the Markdown file in `content/posts/` and add your content.

### 5. Start the Local Server

Run the Hugo development server:

```bash
hugo server --noHTTPCache
```

Visit `http://localhost:1313` to see your site in action.

### 6. Build and Deploy

To generate static files for deployment, run:

```bash
hugo
```

This will create a `public/` folder containing your site's HTML files.

## Hosting on GitHub Pages

To host your Hugo site on GitHub Pages, follow these steps:

### 1. Create a GitHub Repository

1. Go to [GitHub](https://github.com) and log in.
2. Click the **+** icon in the top-right corner and select **New repository**.
3. Enter a repository name: **yourusername.github.io** (replace `yourusername` with your actual GitHub username).
4. Click **Create repository**.

### 2. Create a GitHub Actions Workflow for Deployment

Create a workflow directory and a YAML configuration file in your main Hugo site directory:

```bash
mkdir -p .github/workflows
cd .github/workflows
touch hugo.yaml
```

### 3. Add the GitHub Actions Workflow Configuration

Edit `hugo.yaml` and add the following content:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches:
      - master
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.163.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout Repository
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup GitHub Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js Dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build Hugo Site
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          hugo --gc --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload Site Artifacts
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### 4. Add Files for Your Website


Then add your Hugo-generated site files to the repository, commit, and push:

```bash
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/YOUR_GITHUB_REPO.git
git push -u origin master
```

### 5. Configure the Repository for GitHub Pages

1. Go to the **Settings** tab of your repository.
2. Scroll down to the **Pages** section (**Settings > Pages**).
3. Change the **Source** to **GitHub Actions**.

### 6. Check Deployment Status

1. In your GitHub repository, navigate to **Actions** from the main menu.
2. Look for the workflow named **Deploy Hugo site to Pages**.
3. Once the deployment is complete, the status indicator should turn **green**.

### 7. Verify Your GitHub Pages Site

Your site will be available at:

```
https://yourusername.github.io/
```

(replace `yourusername` with your GitHub username).

## Conclusion

That's how this site runs — Hugo locally, GitHub Pages in production. If you want a simple personal blog without managing a CMS, Hugo is worth trying.
