+++
title = "Connecting a Namecheap domain to Vercel and GitHub Pages"
description = "Step by step: how I pointed a Namecheap domain at a Next.js app on Vercel, and how I put two GitHub Pages sites on my own .me domain and subdomain."
date = 2024-11-30
updated = 2026-10-26
template = "post.html"

[extra]
# The main site holds the original copy of this post
canonical = "https://resume.kaggwemarvinvictor.me/blog/custom-domains-vercel-github-pages/"

+++

> **Update, October 2026:** I did not renew foundug.com, so [FoundUG](https://foundug.vercel.app) is back on its free address, **foundug.vercel.app**. I have also added Part 2, about putting my GitHub Pages sites on my new domain, kaggwemarvinvictor.me, and Part 3, about going back to a free address.

A site on `something.vercel.app` or `username.github.io` works, but your own domain looks more professional and is easier to remember. I have done this twice, both times with a domain bought on Namecheap:

1. **foundug.com** (November 2024) for [FoundUG](https://foundug.vercel.app), a Next.js app on **Vercel**.
2. **kaggwemarvinvictor.me** (October 2026) for my sites on **GitHub Pages**. I got the `.me` domain free for a year from Namecheap through the [GitHub Student Developer Pack](https://education.github.com/pack).

## The idea in one paragraph

A domain is just a name. DNS records tell the internet which server that name points to. You add those records at the place you bought the domain (Namecheap), and you tell the host (Vercel or GitHub) which domain to expect. When both sides agree, the host gives you a free HTTPS certificate.

There are two kinds of records you will use:

- **A record:** points a name to an IP address, like `185.199.108.153`.
- **CNAME record:** points a name to another name, like `kaggwe-marvin.github.io`.

## Part 1: Namecheap to Vercel (foundug.com, November 2024)

### Step 1: Add the domain in Vercel

1. Open your project in Vercel.
2. Go to **Settings → Domains**.
3. Type `foundug.com` and click **Add**. Choose to add `www.foundug.com` too, and redirect it to the main domain.

Vercel then shows the DNS records it wants. They show as "Invalid Configuration" until you add them.

### Step 2: Add the records in Namecheap

1. In Namecheap, go to **Domain List** and click **Manage** next to the domain.
2. Open the **Advanced DNS** tab.
3. **Delete the parking records** Namecheap adds by default (a `CNAME` for `www` pointing to `parkingpage.namecheap.com` and a `URL Redirect` record). If you skip this, they clash with the new records.
4. Add these two records:

| Type | Host | Value |
|---|---|---|
| A Record | `@` | `76.76.21.21` |
| CNAME Record | `www` | `cname.vercel-dns.com.` |

`@` means the domain itself (`foundug.com`). Always copy the values from your Vercel dashboard, since Vercel can change them.

### Step 3: Wait and check

Go back to **Settings → Domains** in Vercel. Within a few minutes to an hour, both domains turn green and Vercel adds HTTPS by itself. That is it: every push to GitHub still deploys as before, now on your own domain.

The free `foundug.vercel.app` address keeps working the whole time. That turned out to matter (see Part 3).

## Part 2: Namecheap to GitHub Pages (kaggwemarvinvictor.me, October 2026)

I have two Zola sites on GitHub Pages. I wanted my notes site on the main domain and my resume on a subdomain:

- `kaggwemarvinvictor.me` → the [kaggwe-marvin-resume](https://github.com/kaggwe-marvin/kaggwe-marvin-resume) repo
- `resume.kaggwemarvinvictor.me` → the [Personal-Resume](https://github.com/kaggwe-marvin/Personal-Resume) repo

### Step 1: Add the records in Namecheap

In **Advanced DNS**, delete the parking records, then add:

| Type | Host | Value |
|---|---|---|
| A Record | `@` | `185.199.108.153` |
| A Record | `@` | `185.199.109.153` |
| A Record | `@` | `185.199.110.153` |
| A Record | `@` | `185.199.111.153` |
| CNAME Record | `www` | `kaggwe-marvin.github.io.` |
| CNAME Record | `resume` | `kaggwe-marvin.github.io.` |

The four A records are GitHub Pages' addresses. The CNAME records point to **your username address**, not the repo name. GitHub works out which repo to show from the domain each repo claims.

### Step 2: Tell each repo its domain

For each repo, go to **Settings → Pages**, type the domain under **Custom domain** and click **Save**:

- `kaggwemarvinvictor.me` in kaggwe-marvin-resume
- `resume.kaggwemarvinvictor.me` in Personal-Resume

If a site is published from a branch (like `gh-pages`), this setting is stored in a file called `CNAME` in that branch. I also keep a `static/CNAME` file in each Zola site, so the domain survives every rebuild.

### Step 3: Update the site's own address

Static site generators build full links from a base address. In Zola that is `base_url` in `config.toml`:

```toml
base_url = "https://kaggwemarvinvictor.me"
```

If you forget this, the pages load but the styles and links still point to the old `github.io` address.

### Step 4: Turn on HTTPS

Back in **Settings → Pages**, wait for the DNS check to pass, then tick **Enforce HTTPS**. If the box is greyed out, GitHub is still making the certificate. Give it up to an hour.

Old `kaggwe-marvin.github.io/...` links keep working. GitHub redirects them to the new domain.

## Part 3: When a domain expires (foundug.com, 2026)

A domain is rented, not owned. I chose not to renew foundug.com, and once it expired the name stopped working. The app itself was fine, because Vercel still served it at foundug.vercel.app. Moving back took three small steps:

1. **In Vercel**, go to **Settings → Domains** and remove `foundug.com` and `www.foundug.com`. Now `foundug.vercel.app` is the main address again.
2. **In your code**, change any place that names the old domain: links, the site URL in your config, and auth or email settings (for example, allowed redirect URLs in Supabase).
3. **Everywhere else**, update the link: your CV, GitHub repo description and portfolio.

Lesson: before you buy a domain, decide whether you will pay for it next year too. If not, keep sharing the free address, so links in old CVs and posts never break.

## Mistakes I made, so you can skip them

1. **Putting the domain on the wrong repo.** I first set the domain on my `username.github.io` repo, which was empty. On GitHub Pages, a domain on that special repo moves *every* project site under it. My resume ended up at `kaggwemarvinvictor.me/Personal-Resume/`. A domain can only belong to one repo, so I had to remove it there before I could add it to the right one.
2. **Leaving the parking records.** Namecheap's default records clash with yours. Delete them first.
3. **Changing `base_url` too early.** If you push the new address before the domain is connected, the site loads without its styles. Connect the domain first, then push.

## Which one is easier?

Vercel is easier: two records, and it guides you through them. GitHub Pages needs a few more records and one extra setting per repo, but it is free and works well for static sites like mine.

Either way, the pattern is the same: **add records at Namecheap, add the domain at the host, wait, then turn on HTTPS.** And set a reminder for the renewal date.
