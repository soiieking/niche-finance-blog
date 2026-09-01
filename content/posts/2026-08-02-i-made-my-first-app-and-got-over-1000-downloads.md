---
title: 'Hitting 1,000 Downloads: How to Actually Host Your First Indie App Without
  Crying'
date: '2026-08-02T18:23:05+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Hitting 1,000 Downloads: How to Actually Host Your First Indie
  App Without Crying.'
---

Someone posted a win on r/sideproject this week. They hit 1,000 downloads for their first app. The thread was mostly high-fives, but buried in the comments was the real story: their database kept crashing from the sudden traffic spike. 
Going from 0 to 1,000 users is a brutal, exciting inflection point. It is exactly when your scratchy DigitalOcean $5 droplet starts sweating. 1,000 users downloading an app means API calls, image processing, and database locks. When you hit that wall, you have three real options for hosting. I have used all three, and broken things on all three.
### The $5 VPS and a Prayer (Hetzner + Docker Compose)
I love DigitalOcean for its UI, but Hetzner is the undisputed king of cheap bum-padding compute. A CX22 Hetzner instance gives you 4GB RAM and 2 vCPUs for about €4.50 a month. The equivalent DigitalOcean droplet costs $24. You are paying $20 a month for a nicer control panel. 
For a 1,000-user app, a single VPS running Docker Compose is almost always enough. It takes 15 minutes to set up. You SSH in, install Docker, copy over your `compose.yaml`, and run it.
The fatal flaw? Zero horizontal scaling. If your app goes viral on Hacker News and your CPU hits 100%, the whole box locks up. You also have to handle your own Postgres backups. If you do not set up a cron job to dump that database to S3, you are one bad update away from losing all 1,000 of your hard-earned users. 
### Managed PaaS (Railway vs Fly.io)
If you do not want to touch Linux permissions, you use a Platform as a Service. This is the "I want to sleep at night" option. Railway and Fly.io are the current favorites. You push a Dockerfile, they build it, and they give you a URL.
I like Railway for rapid prototyping. It is stupidly easy to provision a Postgres instance right next to your Node app. But Railway pricing is a trap. They charge you for execution time, not just instance size. If your API has a background worker polling every 5 seconds, your Railway bill will quietly creep up to $40 a month for an app that barely does anything.
Fly.io is technically superior but operationally weirder. It natively supports fast global deploys out of the box, so if a chunk of your 1,000 users is in Europe, they get a local edge server. But deploying to Fly via their CLI feels slower than it should be, and the community is genuinely split on whether their volume storage for databases is reliable enough for production. I haven't tested this on ARM yet, but Fly's free tier happily runs tiny instances on spare Apple Silicon racks, which is wild.
### Serverless Split (Cloudflare Workers + Neon)
If your app is mostly an API backend for a mobile frontend, skip the server entirely. 
A lot of commenters in the thread suggested moving straight to AWS EC2. Ignore them. AWS is overkill for most people. Your dev environment will break, you will misconfigure a security group, and you will get a $300 surprise bill because you left a NAT Gateway running. 
Instead, put your API on Cloudflare Workers and your database on Neon. Workers run globally on the edge, handle insane concurrency, and the free tier covers millions of requests. Neon is serverless Postgres—it scales down to zero when idle and scales up instantly. If your app spikes because all 1,000 users open it at 9 AM, Neon handles the connection pooling automatically. You will not pay a cent until you hit much higher traffic limits. 
The only catch is the cold start. Neon takes about 300ms to wake up a parked database. Your users will notice this on their first tap of the day. 
### Where do you actually go from 1k?
Do not rewrite your infrastructure just because you hit a milestone. If your Hetzner box is holding up at 60% CPU, leave it alone. Spend that weekend writing code or doing marketing. 
The jump from 1,000 to 10,000 is where you actually need a load balancer. Until then, pick the stack that lets you ship without reading a 40-page Terraform manual.
