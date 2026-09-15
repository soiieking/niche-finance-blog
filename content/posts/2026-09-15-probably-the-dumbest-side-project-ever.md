---
title: This Side Project Is Dumb, and I Built It Anyway
date: '2026-09-15 16:00:04+08:00'
draft: false
tags:
- indie-hacker
- fun
- programming
summary: Some side projects teach you new skills. Others are unabashedly pointless
  and still worth doing. This one was both.
---

## The Dumbest Side Project I Ever Made

A few months ago, I stumbled on an r/sideproject thread titled "Probably the dumbest side project ever..." Naturally, I had to click. The post was about this guy who spent two weeks building an app that just generates random potato facts. That's it. No AI magic, no deep backend wizardry, just potatoes. 

I laughed. Then I immediately started thinking, "Could I one-up this?" And down the rabbit hole I went.

## What *Exactly* Did I Build?

I made a Slack bot that does absolutely nothing useful. It just replies to any message in a channel with an obscure insult from Shakespeare. You're trying to coordinate your team’s sprint, and all of a sudden, the bot chimes in with, "Thou art as fat as butter."

Because why not.

### How It Works (and Why It Barely Does)

The bot's backend is a Node.js script running on a $5/month DigitalOcean droplet (yes, overkill, but Heroku shut down its free tier and I couldn't be bothered to migrate to Fly.io). It's wired up to Slack using Bolt.js, which is straightforward enough until you screw up the Slack app permissions for the 17th time.

All it does is listen for messages in a Slack channel and randomly pull insults from an API I slapped together. The API? That's just a JSON file of about 500 Shakespearean insults stored in an S3 bucket. Total data size: 23 KB. Peak performance, clearly.

It took me about 6 hours to build, most of which was spent Googling “how do I not expose my Slack API token by accident.”

## Why Build This?

Two reasons:

1. **Boredom.** Sometimes you’re burned out on "serious" projects, and you just want to make something without worrying about product-market fit, unit tests, or whether your thing can handle 10k concurrent users.

2. **Because you learn stuff along the way.** Even for a nonsense bot, I had to figure out Slack event APIs, secure environment variables, and basic server config. Reusable skills, even if the end product is absolute trash.

## The Unexpected Outcome: People Actually Used It

Here's the funny part: I added it to a Slack workspace with some friends, and they *loved* it. People started gaming it, saying random stuff just to see what insult they'd get. Within a week, someone even forked my GitHub repo and added a "custom insult mode" where you can upload your own phrases.

By the way, if you're curious: no, this didn't blow up or go viral. It’s not one of those stories. But for a tiny, dumb bot, it got more attention than it deserved. 

## Lessons Learned (Including What I Screwed Up)

### 1. **Get Your Dev Environment Right**
I wasted a solid hour debugging why my bot couldn’t receive events... only to realize I hadn’t tunneled my localhost to Slack using ngrok properly. If you're doing anything with webhooks, set up ngrok (or Cloudflared, if you’re feeling fancy) *right away.*

### 2. **Tech Overkill Is a Real Temptation**
Did this bot really need a dedicated droplet and that whole S3 setup? Nope. I could’ve used a free-tier service or even a Google Spreadsheet as the insult database. But part of this was about playing with tools, so I don’t regret the DigitalOcean splurge… much.

### 3. **Dumb Isn’t Bad**
The best part wasn’t the bot itself but the reactions it got. One friend said it made a miserable Monday in their office slightly better. That alone made this dumb project totally worth it.

## Would I Recommend Building Something So Pointless?

Absolutely. Not every project has to feed into your LinkedIn portfolio. Some should just exist because they’re fun. Or frustrating. Or both.

Just don’t expect to get rich, famous, or even mildly internet-famous from it. And maybe pick a tech stack that doesn't require you to manage your own servers for no reason.

---

### No FAQs Here
If you're asking why I did this, you clearly skipped the part about boredom.
