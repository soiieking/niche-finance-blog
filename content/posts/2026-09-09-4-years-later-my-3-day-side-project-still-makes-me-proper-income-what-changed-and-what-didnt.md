---
title: '3 Days of Work, 4 Years of Income: What Worked, What Didn''t'
date: '2026-09-09 02:00:05+08:00'
draft: false
tags:
- indie-hacker
- side-project
- passive-income
summary: How a weekend side project turned into a reliable income stream for 4 years—and
  the lessons learned from what stayed the same and what broke.
---

Building something quick that lasts this long feels like winning the indie hacker lottery. But it's not magic. Every system has breaking points—even the ones you don't expect. Here’s the breakdown of what stood the test of time, what crumbled, and what this means if you’re building your own side project.

## The Setup: 3 Days, a Micro-Niche, and Good Timing

I built the project in 2019. It’s a SaaS tool aimed at a very small, very specific group of users—basically, people who needed a lightweight alternative to clunky enterprise software. It took me three days to push version 1.0 because I knew what problem I wanted to solve (I was my own customer). I charged $9/month. No free tier. Minimum effort pricing.

It wasn't groundbreaking—the feature set was embarrassingly thin at launch. Think CRUD operations with about 10% more polish than Notion templates. But here's the thing: speed mattered more than perfection. I got it out there, it scratched an itch, and early adopters stuck around.

Numbers-wise, it hit 50 paying users in the first month. It’s now at ~300 subscribers paying $12/month (after one awkward but necessary price bump in 2022). Not “quit your job” money, but $3,600/month for something I barely touch? Not complaining.

## What Changed: The Inevitable Maintenance Tax

1. **Hosting Costs Creeped Up.**  
   I started on DigitalOcean (a $5/month droplet). It was great until I got paranoid about uptime, added monitoring, then backups, then moved to managed Kubernetes (overkill alert). That shot my costs up to $50/month. Still manageable, but a good reminder: simplicity wins unless you *need* complexity.

2. **Support Tickets Increased as More Users Signed On.**  
   When you have 10 users you can reply “it’s on my radar!” and they’ll be nice about it. At 300? People expect answers fast. I migrated support from Gmail to HelpScout and started using pre-written templates for common issues. 15 minutes/day now covers it.

3. **Payment Processor Drama (Thanks, Stripe).**  
   Stripe is amazing until they randomly flag your account for “fraud checks” with no context. In 2021, I had to temporarily pause payments for a week. Lesson learned: always have a backup plan. Paddle or Chargebee could’ve stepped in, but I didn’t prep for it.

4. **The Ecosystem Around Me Changed.**  
   This one’s huge. In 2019, my tool was competing against Excel sheets and slow adoption of Airtable. By 2023, Airtable got faster, Notion stole headlines, and smaller open-source competitors like Baserow started eating into the niche. Did I lose customers? Yes. About 10% churn year-over-year. Am I panicking? Not yet—it’s slow, and most of my users stay loyal because they hate switching tools.

## What Stayed the Same: The Power of Boring Tech

1. **No Bells, No Whistles, No Regrets.**  
   MVP purists have a point: overbuilding kills speed. My app still doesn’t have a mobile version (responsive design suffices). No third-party integrations. No gamified UI/UX. This is overkill for 90% of indie projects. Boring is sustainable.

2. **That $12/Month Pricing? *Solid*.**  
   In the thread someone mentioned they regretted underpricing their SaaS at launch. I think about that every day. $12 is still cheap enough for individuals but adds up for businesses. I debated freemium, but honestly, attracting tire-kickers isn’t worth the hosting load.

3. **My Tech Stack is… Fine.**  
   Backend: a Flask app. Database: PostgreSQL. Deployed via Docker. Zero rewrites in 4 years. I’m convinced there’s “good enough” tech that avoids future headaches, and this is it. Note: I haven’t tested ARM architecture or any fancy optimizations. If you’re chasing scale, this stack will break before you hit 10k users.

## Why It Matters Now

We’re in an odd renaissance of side projects. The tools are cheaper, niches are wider, but customers are also savvier. People aren’t throwing $5/month at “cool experiments” anymore—they want tools that work well, have a clear use case, and won’t disappear in a year.

This is why my 3-day project survived: it didn’t try to be everything. I shipped it fast, listened to early feedback, and spent just enough time maintaining it to keep users happy. On the flip side, I *underestimated* how much tiny changes—like hosting costs, Stripe issues, or competition—can snowball. 

So if you’re launching your own: focus on something narrow, charge what it’s worth, and expect unexpected headaches 2+ years in. Build quickly, but be okay with slow growth.

## Lessons for Builders

1. **Don’t Overthink Hosting.** Hetzner is cheaper than AWS if bandwidth isn’t crushing you. DigitalOcean is fine for small projects but beware of upsell features.
2. **Pricing Is a Commitment.** Undercutting yourself early locks you into bad margins. Have the courage to charge fair prices (you can always offer discounts).
3. **Tech Debt Isn’t Always Evil.** I never rewrote my backend because it worked. Your future self will thank you for choosing tools you can mostly ignore.

That’s it. No grand revelations, just one small project that worked, broke in small ways, got patched up, and kept chugging along. Sometimes the best wins are boring.

--- 

### FAQ

#### How did you find your first users?  
Reddit, honestly. Specifically, subreddits like r/Productivity with a clean no-spam post explaining what the tool did. I also manually emailed people I thought could use it. It wasn’t scalable but worked early on.

#### Why not just run it for free?  
Free users are expensive in terms of server load, support, and churn. Paid users are self-selecting—they need the product enough to spend money. I’d rather deal with 300 paying customers than 3,000 free ones.

#### Why not scale this up if it’s working?  
Scaling is a different game. More users = more support, compliance, and hosting headaches. For now, the “small and steady” approach fits my life better. I might rethink that if churn spikes or revenue stagnates.
