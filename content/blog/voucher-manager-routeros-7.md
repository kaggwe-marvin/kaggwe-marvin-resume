+++
title = "Why I built Voucher Manager: printing hotspot vouchers on RouterOS 7"
description = "MikroTik's User Manager in RouterOS 7 can make vouchers, but it takes five separate steps. Here is what those steps are and how I turned them into one button."
date = 2026-10-25
template = "post.html"

[extra]
# The main site holds the original copy of this post
canonical = "https://resume.kaggwemarvinvictor.me/blog/voucher-manager-routeros-7/"

+++

If you run a Wi-Fi hotspot on a MikroTik router, you sell access with vouchers: small printed cards with a username and password. A customer pays, gets a card, and logs in.

On RouterOS 7 this should be a five-minute job. In practice it is not. That is why I built [Voucher Manager](https://github.com/kaggwe-marvin/Voucher-Manager), a small Windows app that does the whole job in one click.

## What changed in RouterOS 7

In RouterOS 7, MikroTik rebuilt User Manager from scratch. It uses a new database, new menus and new commands, and old setups have to be migrated across ([MikroTik docs](https://help.mikrotik.com/docs/spaces/ROS/pages/2555940/User+Manager)).

The new web page at `/um/` is for customers. They can check their usage and buy a plan. It is not a tool for an operator who wants to print 50 vouchers before the evening rush. For that, you go back to the command line.

## The five steps RouterOS 7 makes you do

To get one sheet of printable vouchers, you have to do all of this:

1. **Create the users.** One `/user-manager user add` per user, with a name, password and group. There is also an `add-batch-users` command that makes random users in bulk, but it does not do the next steps for you.
2. **Give every user a plan.** A user is linked to a profile (for example "1 day" or "1 week") with a separate command, once per user:

   ```
   /user-manager user-profile add user=abc123 profile=1-day
   ```

3. **Generate the vouchers.** Run `generate-voucher` on exactly the users you just made, and name a voucher template that you have already uploaded to the router's Files:

   ```
   /user-manager user generate-voucher numbers=*1,*2,*3 voucher-template=printable_vouchers.html
   ```

4. **Find the output.** The router writes an HTML file to `um5files/PRIVATE/GENERATED/vouchers/gen_printable_vouchers.html`. To open it in a browser, you first need to set a private web username and password in User Manager's advanced settings. Otherwise you copy it off the router another way, such as FTP.
5. **Print it.** Open the HTML file and print it, or save it as a PDF.

None of these steps is hard alone. Together, done by hand for every batch, they are slow and easy to get wrong.

## Where it goes wrong

- **Forgetting a profile.** A user with no profile attached has no plan, so the card you hand over may not work as sold.
- **Generating for the wrong users.** `generate-voucher` needs the internal IDs of the right users. Pick the wrong ones and you print old cards again, or miss new ones.
- **The file is not ready yet.** Right after `generate-voucher` returns, the HTML file may still be being written. Try to download it too early and you get an error.
- **Typing plan names by hand.** One typo in a profile name and the command fails halfway, leaving some users created and others not.

## What Voucher Manager does instead

Voucher Manager talks to the router through its API (port 8728, or 8729 with TLS) and runs every step in order:

1. **Test Connection** checks your login before anything runs.
2. **Refresh** loads your user groups and profiles straight from the router, so you pick them from a list instead of typing them.
3. It creates each user, attaches the profile you chose, and keeps the ID the router returns.
4. It runs `generate-voucher` on exactly those IDs, using your template.
5. It downloads the HTML file over FTP, and retries a few times if the file is not ready yet.
6. It turns the file into a PDF with Microsoft Edge or Chrome, ready to print.

If something fails halfway, it still saves a list of the users it already created next to your PDF. Nothing is left on the router without a record.

You can make up to 500 vouchers per batch, with an optional username prefix so you can tell batches apart.

## What I learned

- **Read the API, not just the menus.** The RouterOS API returns the ID of every user you create. Keeping those IDs is what makes "generate vouchers for exactly this batch" reliable.
- **Plan for slow hardware.** Routers are small computers. Waiting and retrying is part of the design, not a bug fix.
- **Ship it properly.** I set up a GitHub Actions pipeline that builds a Windows installer and a portable `.exe`, with SHA256 checksums, every time I tag a release. People who are not developers can install it like any other app.

## Try it

Voucher Manager is free and open source. It needs Windows 10 or 11, and your router needs the API and FTP services turned on, a voucher template in Files, and at least one profile and user group.

- [Download the latest version](@/projects.md)
- [Source code on GitHub](https://github.com/kaggwe-marvin/Voucher-Manager)

If you run a MikroTik hotspot and try it, I would like to hear how it goes.
