+++ 
draft = false
date = 2025-06-27
title = "How I Built This Site - My Journey with Hugo and the Ordin Project"
description = "A walkthrough of building my portfolio site with Hugo and my experience working on the Ordin project"
slug = "how-i-built-this-site"
authors = []
tags = ["hugo", "web-development", "portfolio", "ordin"]
categories = ["development"]
externalLink = ""
series = ["My Ordin Project"]
+++

## How I Built This Site - My Journey with Hugo and the Ordin Project

Building a personal portfolio site has been on my todo list for ages, but it wasn't until I dived into the Ordin project that I finally made it happen. Here's the story of how this site came together and what I learned along the way.

## Why Hugo?

When I began the Ordin project, I needed to document my progress and showcase my work. I wanted something:

- **Fast** - Static sites load quickly
- **Simple** - Minimal setup and maintenance (When themes click)
- **Flexible** - Easy to customize and extend
- **Developer-friendly** - Markdown support and Git workflow

Hugo checked all these boxes. Coming from working with various frameworks during Ordin development, Hugo's simplicity was refreshing.

## The Setup Process

### Initial Setup

Getting started was surprisingly straightforward:

```bash
# Install Hugo (Windows)
winget install Hugo.Hugo.Extended

# Create new site
hugo new site resume
cd resume

# Initialize Git
git init
git branch -m main development
```

### Theme Selection

I spent considerable time evaluating themes. I wanted something simple for my portfolio. I eventually chose hugo-coder because:

- Clean, professional design
- Mobile responsive
- Active maintenance

### Configuration

The `hugo.toml` configuration was key to getting everything working:

```toml
baseURL = "https://kaggwe-marvin.github.io/kaggwe-marvin-resume/"
title = "Kaggwe Marvin"
theme = "hugo-coder"
languageCode = "en"
defaultContentLanguage = "en"
enableEmoji = true

[pagination]
pagerSize = 6

[services]
[services.disqus]
# Enable Disqus comments
# shortname = "yourdiscussshortname"

[markup]
[markup.highlight]
noClasses = false

[markup.goldmark]
[markup.goldmark.renderer]
unsafe = true

[params]
author = "Kaggwe Marvin"
# license = '<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">CC BY-SA-4.0</a>'
description = "Kaggwe Marvin's personal website"
keywords = "blog,developer,personal"
info = ["Indie Hacker"]
avatarURL = "images/avatar.jpg"

dateFormat = "January 2, 2006"
since = 2021
# Git Commit in Footer, uncomment the line below to enable it
commit = "https://github.com/luizdepra/hugo-coder/tree/"
# Right To Left, shift content direction for languages such as Arabic
rtl = false
# Specify light/dark colorscheme
# Supported values:
# "auto" (use preference set by browser)
# "dark" (dark background, light foreground)
# "light" (light background, dark foreground) (default)
colorScheme = "auto"
# Hide the toggle button, along with the associated vertical divider
hideColorSchemeToggle = false
# Series see also post count
maxSeeAlsoItems = 5
# Custom CSS
customCSS = []
# Custom SCSS, file path is relative to Hugo's asset folder (default: {your project root}/assets)
customSCSS = []

# Custom JS
customJS = []

# Custom remote JS files
customRemoteJS = []



[taxonomies]
category = "categories"
series = "series"
tag = "tags"
author = "authors"

[[params.social]]
name = "Github"
icon = "fa-brands fa-github fa-2x"
weight = 1
url = "https://github.com/kaggwe-marvin/"

[[params.social]]
name = "Twitter"
icon = "fa-brands fa-x-twitter fa-2x"
weight = 3
url = "https://twitter.com/kaggwe-marvin/"

[[params.social]]
name = "LinkedIn"
icon = "fa-brands fa-linkedin fa-2x"
weight = 4
url = "https://www.linkedin.com/in/kaggwe-marvin/"




[languages.en]
languageName = ":uk:"

[[languages.en.menu.main]]
name = "About"
weight = 1
url = "about/"

[[languages.en.menu.main]]
name = "Blog"
weight = 2
url = "posts/"

[[languages.en.menu.main]]
name = "Projects"
weight = 3
url = "projects/"

[[languages.en.menu.main]]
name = "Contact me"
weight = 5
url = "contact/"

[caches]
  [caches.images]
    dir = ':cacheDir/images'
```

## Content Structure

I organized the site to highlight both my general skills and my specific work on Ordin:

- **About** - Personal background and technical skills
- **Projects** - Featured projects including Ordin
- **Blog** - Technical posts and project updates
- **Contact** - Ways to reach me

## The Ordin Connection

Working on the Ordin project taught me several things that directly influenced how I built this site:

### Documentation Matters

Ordin required extensive documentation, which made me appreciate good content organization. I applied the same principles here - clear structure, searchable content, and regular updates.

### Iterative Development

Just like with Ordin, I launched this site early and improved it incrementally. The initial version was basic, but it was live and functional.

## Deployment Process

I chose github pages for hosting:

```bash
# Build the site
mkdir -p .github/workflows
touch .github/workflows/hugo.yaml

# copy and paste
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.147.9
      HUGO_ENVIRONMENT: production
      TZ: America/Los_Angeles
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Cache Restore
        id: cache-restore
        uses: actions/cache/restore@v4
        with:
          path: |
            ${{ runner.temp }}/hugo_cache
          key: hugo-${{ github.run_id }}
          restore-keys:
            hugo-
      - name: Configure Git
        run: git config core.quotepath false
      - name: Build with Hugo
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/" \
            --cacheDir "${{ runner.temp }}/hugo_cache"
      - name: Cache Save
        id: cache-save
        uses: actions/cache/save@v4
        with:
          path: |
            ${{ runner.temp }}/hugo_cache
          key: ${{ steps.cache-restore.outputs.cache-primary-key }}
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
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
# Commit and push for the Github Action to build and deploy the site
git add -A
git commit -m "Create hugo.yaml"
git push
```

The automated deployment pipeline meant I could focus on content rather than infrastructure - a lesson learned from CI/CD

## Lessons Learned

1. **Hugo's Themes** - Just toxic nothing works without the other
2. **Simplicity** - I'd choose simplicity any day to get small tasks.

### What Worked Well

1. **Starting simple** - Basic setup got me online quickly
2. **Content-first approach** - Focused on writing good content over complex features
3. **Regular updates** - Treating it like a living project, not a one-time build

### What I'd Do Differently

1. **Theme research** - Spend more time evaluating options upfront
2. **Content planning** - Better organization of posts and projects from the start
3. **Analytics setup** - Implement tracking earlier to understand usage

## The Ordin Impact

Working on Ordin fundamentally changed how I approach projects:

- **Scalable architecture** - Building for growth from day one
- **Continuous learning** - Embracing new technologies and methodologies

These principles shaped not just this website, but my entire development philosophy.

## What's Next?

This site will continue evolving alongside my work on Ordin and other projects. Planned improvements include:

- Enhanced project showcase
- Technical deep-dive posts
- Interactive demos
- Performance optimizations

The journey of building this site paralleled my growth as a developer through the Ordin project - both taught me the value of starting simple, iterating quickly, and always keeping the user in mind.

---

*This site's source code is available on [GitHub]<https://github.com/kaggwe-marvin/kaggwe-marvin-resume>. Feel free to reach out if you have questions about Hugo, the Ordin project, or web development in general.*
