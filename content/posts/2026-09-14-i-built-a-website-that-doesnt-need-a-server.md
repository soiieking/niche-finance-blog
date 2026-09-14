---
title: I Built a Website That Doesn't Need a Server
date: '2026-09-14 18:00:05+08:00'
draft: false
tags:
- indie-hacker
- technology
- web-development
summary: How I built a website that runs entirely on static files—no servers, no databases,
  no nonsense.
---

## A Website Without a Server? Yes, Really. 

Here’s the deal: servers are overkill for a lot of things. I wanted to see how much complexity I could strip out while building a working, usable website. The result? A fully functional, fast-loading site with **zero server-side code**. Everything runs on plain static files, served directly from a CDN. And no, I'm not just talking about "bro, I put my HTML on S3." It’s a bit smarter than that. Let me break it down.

## Why Build This?

For starters, simplicity. Servers bring fun headaches: you’ve got security patches, scaling issues, SSH sessions that break just because Mercury is in retrograde, etc. If you’re hosting a blog, a portfolio, or a small product site, all that overhead feels wasteful. Seriously, who needs dynamic backends for a contact form or product landing page?

Static sites, by contrast, are stupid-simple. Short setup time, fewer moving parts, almost no attack surface. For most indie hackers, that’s a win. Plus, with modern tools like **Netlify** and **Cloudflare Pages**, static sites play well with JAMstack if you do need some dynamic magic.

## The Tech Stack for This

I used **11ty** (Eleventy) as the static site generator. If you’ve ever tried it, the pitch is clear: **"zero config, just plain HTML if you want."** I didn't want to waste time wrestling with React (though you *could* use something like Next.js if you want SPAs). The flow looked like this:

1. Markup, CSS, and a tiny sprinkle of JavaScript.
2. Processed into static files with 11ty.
3. Hosted entirely on **Cloudflare Pages**, because free tier + screaming-fast global CDN ≠ regrets. 

No server, no database, no problem.

Here’s a highlight: my website clocks in at **under 3 MB**, with a 99th percentile load time of ~500ms globally, even in far-off regions like Jakarta. That’s insanely good for 2026 web standards.

Now, let’s talk about the "but what abouts."

### How I Handle Dynamic Stuff (Without a Backend)
This was the point everyone raised on Reddit (including user `codeFox99`). "Cool static site, but what happens if you need forms?" Totally fair.

For forms, I used **Formspree**. It's a little third-party service that processes your form submissions, sends email notifications, and lets you handle spam. Is it overkill for this? Yeah, but it works well.

For things like search, I took the lazy route: **client-side JavaScript search.** Pull down a JSON index of the content, and filter it locally. Works fine for small sites (mine has ~40 blog posts). For big sites, check out Algolia or CloudCannon.

Membership or anything requiring user data—just ship that out to Auth0 or Firebase. Done.

## So, Is This For Everyone?

Short answer: no. If you’re running a SaaS with a billion API calls per second, this approach ain’t it. You need a server. You’ll be fighting this setup every day.

But for static blogs? Portfolios? Event pages? Even newsletters if you’re clever with Zapier? This works. And it’s cheap as hell. 

Here’s some math: the Cloudflare Pages free tier gives you 500 builds and 100GB/month egress for $0. **Compare that to $5/month for a low-tier VPS (Linode/DigitalOcean), plus domain, plus SSL setup.** Servers aren’t killing your wallet, but static sites are essentially *free* in this scenario. 

If your project fits, there’s no excuse not to try.

---

## TL;DR

I made a website that doesn’t need a server. Static files, powered by Eleventy, hosted on Cloudflare Pages. It’s fast, cheap, and easy. Dynamic stuff (forms, search, etc.) handled by third-party services. For anything lightweight that doesn’t involve custom APIs or databases, I think this is the future. 

### FAQ

#### Why not just use WordPress?

WordPress is great if you want quick plugins or non-techies running your site. But it introduces maintenance headaches, performance hits, and security concerns you won’t get with static sites. Static wins for minimalism.

#### Is 11ty better than Hugo or Jekyll?

It depends. I chose 11ty because it’s plain JS, simple to extend, and doesn’t force “The React Way.” Hugo would be faster to build large sites, and Jekyll plays nicest with Ruby. Pick your poison.

#### What’s the catch?

The moment you outgrow static constraints (e.g., complex user interaction, real-time stuff), you’re looking at re-introducing servers. For now, static gets you ~80% there with 0% headache. Keep it simple.
