+++
date = '2020-12-03T17:07:58+05:00'
title = 'Hugo: The Fastest Static Site Generator'
tags = ['hugo', 'github']
+++

## What is Hugo?

Hugo is an open-source static site generator (SSG) written in Go. It is designed for speed, simplicity, and flexibility, making it a popular choice for building blogs, documentation sites, and personal portfolios. Unlike traditional content management systems (CMS) like WordPress, Hugo generates static HTML files that can be served directly from any web server, eliminating the need for a database.

## Why Use Hugo?

Hugo is widely used due to its numerous advantages:

- **Blazing Fast** - Hugo can generate a complete website in milliseconds, making it one of the fastest static site generators.
- **Easy to Use** - Content is written in Markdown, making it simple and portable.
- **Flexible and Customizable** - Supports various themes, templates, and shortcodes.
- **Secure** - No database or dynamic scripts, reducing the risk of security vulnerabilities.
- **SEO-Friendly** - Static HTML is fast and optimized for search engines.
- **Portable** - Websites can be hosted anywhere, including GitHub Pages, Netlify, or a simple web server.

## How to Set Up Hugo

Follow these steps to install and set up Hugo on your system.

### 1. Install Hugo

#### On Linux (Manjaro and Arch-based Systems)
```bash
sudo pacman -S hugo
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
      HUGO_VERSION: 0.147.9
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

Hugo is an excellent choice for anyone looking to build fast, secure, and customizable websites without the overhead of a traditional CMS. Its simplicity and performance make it a favorite among developers and content creators alike.
