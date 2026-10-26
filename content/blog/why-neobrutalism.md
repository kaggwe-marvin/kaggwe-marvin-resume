+++
title = "Why I'm drawn to neobrutalism, and how I toned it down"
description = "Thick borders, hard shadows and no fades: why neobrutalist web design appeals to me as a developer, and the rules I set to make it calm enough for a portfolio."
date = 2026-10-26
template = "post.html"
+++

Most websites today look the same. Soft gradients, rounded corners, blurry shadows and buttons that fade in slowly. It looks polished, but it is hard to tell one site from another.

Neobrutalism goes the other way. Flat colours, thick black borders, sharp corners and shadows with no blur. Nothing is hidden, and nothing pretends to be glass. When I rebuilt this site, I chose it on purpose. Here is why.

## It shows the structure

Brutalism started in architecture. Brutalist buildings leave the raw concrete showing instead of hiding it behind paint. Neobrutalism does the same for the web: every box has a visible border, so you can see how the page is built.

As a developer I like that. Every element is a box anyway. This style stops hiding it.

## It is honest about what you can click

A button here looks like a button. It sits on a hard shadow, and when you press it, it sinks into that shadow like a real key. Hover over it and it turns amber at once, with no slow fade.

That instant change is on purpose. A 300 ms fade feels smooth, but it also makes the page feel slow. A snap feels like a switch. You know right away that the site heard you.

## It is cheap to load

Flat colours and solid borders are simple CSS. No background images, no heavy blur effects, no animation library. The whole stylesheet for this site is one small file.

That matters to me. Many people here in Kampala browse on mobile data, and every kilobyte costs money. A style built from borders and flat fills loads fast on a slow connection.

## Where I toned it down

Classic neobrutalism is loud: bright yellow, pink and cyan, all on one page. My first version of this site used yellow and cyan, and it felt more like a toy than a portfolio. So I set some rules:

1. **Muted colours.** A dark charcoal background, steel-grey cards and one accent: a muted amber. One accent means your eye knows where to look.
2. **Sharp corners only.** No rounded corners. The grid stays clean.
3. **Instant states.** Hover and press change at once. No transitions.
4. **Big blocks stay calm.** Buttons fill with amber on hover. Cards only change their shadow, because flashing a whole card of text amber is tiring to read.
5. **Contrast first.** Every text colour has to pass the WCAG contrast check. That is why text on amber is always dark, never white.

## What it taught me

Picking a strong style does not mean following it blindly. My first plan had shadows in near-black on a near-black background. They looked fine in a mock-up and vanished on a real screen. I measured the contrast, changed the shadow to a lighter slate, and the boxes came back.

A design system is a set of rules you test, not a set of rules you obey. Neobrutalism gave me a clear starting point. Checking it on real screens made it mine.

---

The CSS for this site is [on GitHub](https://github.com/kaggwe-marvin/kaggwe-marvin-resume/blob/main/static/css/style.css) if you want to borrow it.
