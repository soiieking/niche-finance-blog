---
title: I Built a Website I Genuinely Hope Nobody Ever Needs
date: '2026-08-11T10:00:09+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding I Built a Website I Genuinely Hope Nobody Ever Needs.
---

Last month I launched a website that compares funeral home pricing in my state. I spent 60 hours on it. I paid $14 for the domain and $6 a month for a Hetzner VPS. I genuinely hope nobody ever uses it.
That sounds insane. Let me explain.
## The origin story is morbidly practical
It started with a Reddit thread in r/personalfinance. Someone's grandmother died, and the funeral home quoted them $11,000 for a basic service. The comments were full of people who paid wildly different prices for identical services. One guy paid $3,200 in Ohio. Another paid $9,800 in California for the same casket and service package.
The funeral industry is famously opaque. The FTC requires itemized price lists, but you have to call or visit in person to get them. There's no Yelp for death. No Angie's List for burials.
So I built one. Scraped public price lists, called 47 funeral homes pretending to be a grieving relative, and entered the data into a simple Hugo static site with a searchable table. Total cost: about $80 and one very awkward week of phone calls.
## The technical reality check
Here's where it gets interesting. The site is dead simple. Static HTML, a JSON file, and a bit of vanilla JavaScript for filtering. No React. No database. No Docker containers. It runs on a single Hetzner CX22 instance with 2GB of RAM, and I'm pretty sure it uses about 40MB of that.
I could have over-engineered this. I've seen people on r/sideproject spin up Kubernetes clusters for a landing page. This thing is a glorified spreadsheet with a domain name.
The hardest part wasn't the code. It was the data. Funeral homes don't want to give you their prices. I got hung up on 12 times. One owner told me, "If you have to ask, you can't afford it." That's a real quote. I wrote it down.
## Why I hope it fails
Here's the uncomfortable truth: if my site becomes popular, it means a lot of people are dying. The traffic spike I'd need to monetize this thing is directly correlated with human grief.
I've thought about this a lot. The community on r/sideproject is genuinely split on this. Some people say "build it anyway, you're providing a public service." Others say "this is morbid and you should feel bad." I've gotten both in my DMs.
The math is brutal. Funeral costs average $8,300 in the US. If my site saves one family $2,000 by showing them a cheaper option, that's real money. But I can't advertise this. I can't do SEO for "funeral home prices near me" without feeling like a vulture. I haven't even submitted it to Google Search Console.
## What I actually learned
This project taught me more than any SaaS I've built. Here's the honest breakdown:
**The data is the product, not the code.** Anyone can build a table. Nobody wants to make 47 awkward phone calls. That's the moat.
**Static sites are underrated.** This thing loads in under 200ms on a $6 VPS. My last React app took 3 seconds on DigitalOcean's $20 droplet. I'm not saying static is always better, but for content-heavy sites, it's not even close.
**The market fit question is different here.** Usually you ask "does anyone want this?" Here I'm asking "do I want anyone to need this?" That's a weird place to be.
## The fatal flaw
I'll be honest about the biggest problem: I haven't tested this on ARM. The Hetzner box is x86, and I have no idea if the site breaks on a Raspberry Pi. That's a real gap, and I'm not sure I care enough to fix it.
Also, the data goes stale. Funeral homes change prices. I'd need to re-call all 47 places every six months to keep this accurate. That's not sustainable. I'm already dreading it.
## The bottom line
I'm keeping the site up. It costs me $6 a month and a few hours of maintenance. If one family finds it and saves money during the worst week of their life, that's worth it.
But I'm not going to promote it. I'm not going to monetize it. I'm not going to pretend this is a business. It's a public utility that I hope stays irrelevant.
That's the weirdest part of this whole thing. I built something useful, and I'm rooting for it to fail. If you're building a side project, ask yourself: would you be okay with that? Because most of you won't be. And that's fine. But it's worth knowing before you start.
The site is live. The data is accurate as of last month. And I genuinely hope you never need to visit it.
