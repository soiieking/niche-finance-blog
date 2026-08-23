---
title: "From Zero to Your First Users: Distribution for Side Projects That Don't Have an Audience"
date: 2026-08-21T00:00:30+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "You built it, now what? A brutal, practical guide to getting your first users without a following."
---

You built a thing. A CLI tool, a SaaS dashboard, a simple utility. You're staring at your deployment logs, and the only traffic is you. The classic r/sideproject cold start problem.

A user over there, `u/trapped_dev_77`, put it perfectly: *"I have a product that solves a real problem for me. But my 'network' is 12 people on LinkedIn, none of whom care. Where do I even start?"*

Here's the playbook, derived from what actually worked in that thread, not theoretical marketing advice.

## Start Where the Pain Is, Not Where You Are

First, forget your own platform. Your GitHub README, your landing page—those are for people who already know you exist. They're the last step, not the first.

Go to where your potential users are already complaining. This isn't about spamming links. It's about finding the conversation and offering a solution.

**For developers (your likely audience):**
- **GitHub Issues:** Find popular open-source tools your project complements. Look for open issues or feature requests it solves. Write a thoughtful comment. Example: *"This has been a pain point. I built a small utility to handle this exact workflow. Here's a link to a gist/demo if it helps."* Don't just drop the product link.
- **Hacker News "Show HN":** You need a good title. "Show HN: I built a terminal tool for X" works. Then, be **insanely** present in the comments for the first 4 hours. Answer every question, even the snarky ones. The algo loves engagement.
- **Niche Subreddits:** Find the *exact* subreddit for your problem space, not just r/SideProject. r/dataengineering for a data tool, r/selfhosted for a homelab utility. Lurk first. Understand the vibe. Then post a "How I solved X" story, mentioning your tool as part of the solution.

## The "Hacker News" vs. "Reddit" Launch: A Tactical Breakdown

These are two very different beasts. The community is split on this, but here’s my read:

**Hacker News (Show HN):**
- **Goal:** Credibility, backlinks, and a *potential* surge of technical users.
- **Truth:** A "death hug" of traffic will crash your $5/mo DigitalOcean droplet. Be ready. Have a status page. The traffic lasts about 12 hours, then it's gone. Its real value is the permanent link and SEO juice from a domain with 98/100 authority.
- **Tool check:** Use a proper stack. Your frontend should be on Vercel/Netlify, not your single server. Backend on Hetzner Cloud (4 vCPU, 8GB RAM for €15/mo, crushes DigitalOcean's $48 equivalent). Use Plausible Analytics (€9/mo) over Google Analytics—it's faster and respects privacy, which this crowd loves.

**Reddit:**
- **Goal:** Direct feedback, finding your first 10-20 *true* users, and iterative development.
- **Truth:** Slower burn. You might get 3 upvotes but 2 deeply engaged comments. These people will tell you exactly what's wrong with your onboarding. This is infinitely more valuable than a traffic spike.
- **A fatal flaw with Reddit:** Self-promotion rules are strict and enforced by communities, not just bots. You **must** lead with value. Post a tutorial, a benchmark, a comparison. "I tried 5 tools for X, here's what I learned" is the format. Your tool is *one of the five*.

## The Minimum Viable Distribution Stack

Stop overthinking the marketing stack. Here's what you actually need, with real costs:

1.  **A 1-Minute Landing Page:** Use **Carrd** ($19/year) or **Typedream**. Your goal is to capture an email. Not "learn more." Say exactly what the product does in the headline. "Get a daily digest of your AWS costs in Slack."
2.  **A Feedback Channel:** A public **GitHub Discussions** tab or a **Canny** board (free tier). This is where your first users become collaborators.
3.  **Simple Analytics:** **Plausible** or **Umami** (self-hostable). Know where your traffic comes from. Is it that one HN comment? Double down there.

Total cost: <$30/year.

## The Uncomfortable Truth About SEO

It's overkill for most early-stage projects. I love SEO, but it's a 6-12 month game. By the time you rank, your product might pivot entirely. Get your first 100 users through direct, human-to-human channels first. Then, and only then, start writing 2-3 high-quality blog posts answering specific questions your users have asked.

## FAQ

**Q: What if my product isn't for developers? Where do I distribute?**
**A:** Go to where the *end users* are, not builders. A tool for podcasters? Go to r/podcasting, podcast Facebook groups, or indie podcasters' newsletters. The principle is the same: find the pain, offer a solution in-context.

**Q: Should I build in public on Twitter/X?**
**A:** It can work, but it's high-effort. The upside is attracting potential collaborators. The downside is that it's a black hole for time. I've seen better traction from a single, well-placed Show HN than from a month of daily tweets. If you do it, share milestones, not just screenshots. "Just shipped v1.1, it now does X. Here's how I built it."

**Q: I got some users but they all churned. Now what?**
**A:** Distribution got you in the door. Churn means the door was the wrong color or led to a broken room. This is now a product problem, not a distribution problem. Email those churned users. Offer a $20 Amazon gift card for a 15-minute chat. Ask: "What were you hoping to do that didn't work?" The answer will hurt, but it's the roadmap.