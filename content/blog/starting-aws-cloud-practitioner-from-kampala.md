+++
title = "Starting AWS Cloud Practitioner from Kampala"
description = "A business student's 90-day plan"
date = 2026-10-27
template = "post.html"

[extra]
# The main site holds the original copy of this post
canonical = "https://resume.kaggwemarvinvictor.me/blog/starting-aws-cloud-practitioner-from-kampala.md/"

+++
I'm Kaggwe Marvin Victor, a final-year Bachelor of Business Computing student at Makerere University Business School (MUBS) in Kampala, Uganda. I graduate in February 2027. I've built and shipped web apps for a few years: a Django ERP with a double-entry ledger, a Windows app that prints MikroTik hotspot vouchers, and FoundUG, a platform for returning lost documents to their owners in Uganda. So far my web projects have run on GitHub Pages, Vercel and Supabase.

I've never deployed anything to AWS. This week I verified as a student on Builder Center, and I'm going for the **AWS Certified Cloud Practitioner** before I graduate. This post is my plan. I'm writing it down in public so I stick to it.

## Why Cloud Practitioner, and why now

- **It fits a business degree.** The exam isn't only about servers. A big part of it is how cloud pricing works, the shared responsibility model and why companies move to the cloud. That's the kind of thing my course covers from the business side.
- **Student Rewards covers the cost.** Earning 21 badges here gets a $100 voucher for a Foundational exam. For a student in Uganda, $100 is a lot of money, so this really matters.
- **Skill Builder Premium.** Verified students get 12 months of it. That includes the official practice material, so I don't need to pay for a third-party course.

## What the exam covers

The CLF-C02 exam is 65 questions in 90 minutes, and the pass mark is 700 out of 1,000. The four domains are:

| Domain | Weight | How I feel about it |
|---|---|---|
| Cloud Concepts | 24% | Comfortable with the theory, not the AWS terms yet |
| Security and Compliance | 30% | My weakest area: IAM is new to me |
| Cloud Technology and Services | 34% | The biggest chunk. Lots of service names to learn |
| Billing, Pricing and Support | 12% | My accounting units should help here |

Security and services make up almost two thirds of the exam, so that's where most of my hours will go.

## The plan: about 90 days

I'm also studying for an SAP Financial Accounting exam this semester, so AWS gets a steady few hours a week rather than a cram.

**Weeks 1–4: foundations**
- Finish *AWS Cloud Practitioner Essentials* on Skill Builder.
- Before I touch anything, set up a billing alarm and a budget. Rule one is no surprise bills.
- Create an IAM user for myself and stop using the root account.

**Weeks 5–8: build something real**
- Host one of my existing projects on AWS. I'll start with a static site on S3 and CloudFront, then move up to running a Django app.
- Write up what I learn each week here on Builder Center.

**Weeks 9–11: practice and review**
- Take the official practice exam on Skill Builder and list every question I get wrong.
- Re-study the weak domains, which for me will probably be security and the services list.

**Around day 90: the exam**
- Claim the voucher, book online proctoring, and run the system check the day before. My internet setup needs a backup plan, which is a very Kampala problem.

## Things I'm curious about as a Ugandan builder

- The nearest AWS Region to me is Cape Town (`af-south-1`). How much does latency matter for apps whose users are in East Africa? Should I just use a European Region?
- How do small businesses here pay for cloud services when most people use mobile money rather than cards?
- What's the cheapest sensible setup for a small business app that has to stay up?

## Follow along

I'll post an update every week: what I studied, what I built and what confused me. If you've passed Cloud Practitioner, **what's one thing you wish you'd known before the exam?** And if you're in East Africa and building on AWS, which Region do you use, and why?
