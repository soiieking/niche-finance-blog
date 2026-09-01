---
title: 'r/sideproject Year-End Wrap: The Projects We Actually Finished (And Why)'
date: '2026-07-31T15:39:54+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding r/sideproject Year-End Wrap: The Projects We Actually Finished
  (And Why).'
---

Every December, r/sideproject fills up with year-end retrospectives. Most of them are painful reads. People listing 14 abandoned SaaS attempts and blaming their attention spans. 
But a few threads actually cut to the bone. Someone asked: *"As the year wraps up: what’s the project you’re most proud of building and why?"* The answers are a masterclass in what actually matters when you're building stuff on nights and weekends.
It’s not the tech stack. It’s the friction you eliminated.
## The "Ugly But Profitable" Dashboard
The most upvoted response came from u/stack_tracer, who built an internal inventory dashboard for his brother’s landscaping business. Seriously.
He wrote: *"It looks like absolute garbage. I used vanilla PHP and a SQLite database. No framework, no Vue, no Tailwind. But it saved my brother 6 hours a week in Excel data entry, and I learned more building this than I did in 4 failed React SaaS apps."*
I love this take. We spend way too much time optimizing our Vite configs when a 200-line PHP script on a $4 Hetzner box does the job. DigitalOcean is great, but if you just need to host a basic CRUD app, Hetzner’s CX22 gives you 4GB RAM for roughly €4.50 a month. DO charges $6 for 1GB. For side projects, that’s a massive difference. 
## The API That Actually Made Money
Then there was u/quantum_dev. They scraped public county recorders for new business filings and sold the cleaned, geocoded data via a simple REST API. 
This is a classic indie hacker play that actually works. They used Next.js 14 for the marketing site and a Python FastAPI backend for the scraper. 
The most interesting part was the infrastructure: they ditched Docker entirely. *"Docker was eating 800MB of RAM just sitting there idling. I moved to raw systemd services on Ubuntu and my Droplet stopped crashing."* 
Honestly, Docker is overkill for most people. If you have a single Python script pulling JSON every 15 minutes, Podman is a fine alternative, but raw systemd on a $5 VPS is even better. RAM is a finite resource when you're too cheap to scale vertically.
## The Honest Failures
Not everything was a win. u/dev_failures shared a brutally honest post-mortem of their abandoned Discord bot. 
*Found out the hard way that Vercel's Hobby tier kills your WebSocket connections after 30 seconds of idle time."* They hit a wall trying to figure out bot presence syncing, sank three months into Node v20 latency issues, and gave up.
The community is genuinely split on where to host real-time apps. Some folks in the thread were pushing Fly.io, but I’ve seen horror stories about slow cold starts on their free tier. If you need persistent WebSockets, Render or a cheap bare-metal box is usually the safer bet. Vercel is strictly for stateless frontends. 
I haven't tested this specific Discord bot architecture on ARM yet, but switching your VPS from AMD to an Ampere ARM CPU usually halves your hosting bill if your language supports it.
## Stop Building Infrastructure
The overarching theme from this year's r/sideproject threads is simple. Stop building infrastructure. Stop writing custom authentication. Stop designing your own PostgreSQL replication wrappers. 
If your project is a front-end for an LLM, don't ask users to bring their own OpenAI keys—that destroys conversion. Hide the keys, proxy the requests, and eat the token costs until you hit a usage cap.
Ship the ugly PHP app. Write the Go binary that runs in systemd. Stop chasing the next big framework and just finish the stupid thing. Your mileage may vary, but nobody buys the engineering stack—they buy the solution to their headache.
