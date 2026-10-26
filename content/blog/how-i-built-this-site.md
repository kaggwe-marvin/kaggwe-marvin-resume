+++
title = "How I built this site: from Hugo to Zola"
description = "How I first built this site with Hugo and the hugo-coder theme, why I later moved it to Zola, and what I learned both times."
date = 2025-06-27
updated = 2026-10-25
template = "post.html"
aliases = ["posts/how-i-built-this-site/"]

[extra]
# The main site holds the original copy of this post
canonical = "https://resume.kaggwemarvinvictor.me/blog/how-i-built-this-site/"

+++

> **Update, October 2026:** this site no longer runs on Hugo. I rebuilt it with [Zola](https://www.getzola.org/) and my own small template. The second half of this post explains why.

Building a personal site had been on my to-do list for ages. Working on the Ordin project finally pushed me to do it, because I needed a place to document my progress and show my work.

## Version 1: Hugo and hugo-coder

I wanted a site that was:

- **Fast:** static pages load quickly, even on a slow connection
- **Simple:** little setup and little to maintain
- **Flexible:** easy to change later
- **Developer-friendly:** posts in Markdown, changes tracked in Git

Hugo met all four. Setup on Windows took a few commands:

```bash
winget install Hugo.Hugo.Extended
hugo new site resume
cd resume
git init
```

I picked the **hugo-coder** theme because it was clean, worked on phones and was still maintained. A GitHub Actions workflow built the site and published it to GitHub Pages on every push, so I could focus on writing instead of servers.

## What I learned from version 1

1. **Themes are a package deal.** A Hugo theme brings its own layouts, styles, scripts and settings. Changing one small thing often meant learning how all the others fit together.
2. **Simple wins.** For a small personal site, I would choose simplicity every time.
3. **Ship early, then improve.** The first version was basic, but it was live. That beat waiting for perfect.

Working on Ordin taught me the same lessons: documentation matters, and small steps beat big rewrites.

## Version 2: Zola and my own template

A year later the site needed a real update. My CV had changed, I had new projects, and the theme carried a lot of code I did not use. So I rebuilt it:

- **Zola instead of Hugo.** Zola is one small program, and its templates are easy to read.
- **My own template instead of a theme.** Now every line of HTML and CSS is mine and does something.
- **Content as data.** My work history, projects and websites live in simple TOML lists. Updating my resume means editing a list, not HTML.
- **Live downloads.** The Downloads tab reads my latest GitHub releases in the browser, so a new release shows up without rebuilding the site.

The result loads faster, has light and dark modes, prints cleanly, and is easier for me to keep up to date.

## What I would tell myself at the start

1. **Look past the screenshot when choosing a theme.** Check how much code you are taking on, not just how it looks.
2. **Plan your content first.** Decide what pages you need before choosing tools.
3. **Keep old links working.** When I moved to Zola, I kept the old post addresses, so links people shared still open the right page.

---

The source code for this site is [on GitHub](https://github.com/kaggwe-marvin/kaggwe-marvin-resume). Feel free to reach out if you have questions about static sites or web development.
