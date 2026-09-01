---
title: 'Now Your Pile of Hoard Can Fly: Why I Finally Stopped Paying DigitalOcean'
date: '2026-08-02T20:25:05+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Now Your Pile of Hoard Can Fly: Why I Finally Stopped Paying
  DigitalOcean.'
---

Saw a post on r/sideproject this week titled "Now your pile of hoard can fly." It got 300 upvotes in an afternoon. The premise is familiar to anyone who has ever lived in the terminal: you have built 14 micro-SaaS projects and half a dozen Discord bots, and they are scattered across a graveyard of $5/month DigitalOcean droplets. You are paying $90 a month for things that generate $3.12 in Stripe revenue.
I have been there. My home server was literally a Nextcloud instance, a Gitea instance, three dead Telegram bots, and a 2018 Raspberry Pi choking on a 1080p security camera stream. 
Moving all of this to a single $14 Hetzner box was the best infrastructure decision I made last year. But the way the community talks about migration is completely disconnected from reality.
### The Docker Compose Trap
The top comment on the thread was a heavily upvoted recommendation to just spin up a Nomad cluster with Consul for service discovery. Look, I love over-engineering my side projects as much as the next guy, but Nomad is insanity for this use-case. If you are running a hoard of side projects, you do not need distributed service discovery. You have a single VPS in Frankfurt. Just use Docker Compose.
My current setup lives in one 400-line `docker-compose.yml` file. It handles my DNS challenge for Traefik, spins up my PostgreSQL databases, and routes traffic to a Hugo static site, Umami analytics, and a Uptime Kuma instance running 5-minute pings on all my domains. Total RAM footprint sits around 1.2GB on a 4GB instance. Compose is boring. Boring is good. If your reverse proxy requires a master's degree to configure port forwarding, you are doing indie hacking wrong.
### Container Runtimes Are a Religious War
I tried switching the whole stack from Docker to Podman a few months ago because I bought into the daemonless hype. It was a disaster. 
Podman 4.4 still struggles with some specific Macvlan networking quirks that I rely on for my internal home network routing, and Podman Compose is practically a joke in terms of parity with the standard Docker Compose specification. I spent an entire Saturday afternoon debugging why my n8n worker container couldn't resolve local DNS names, only to realize the rootless networking stack just didn't handle custom bridge subnets the same way.
I switched back to standard Docker 24.0. Your mileage may vary, but if you just want your pile of junk online without a fight, stick to Docker until you actually hit a hard scaling wall. Which, for a side project hoard, you never will.
### Don't Host Your Own Database
I cannot stress this enough. Do not run your production PostgreSQL database on the exact same hardware container as your web scraper that reads untrusted RSS feeds.
I learned this the hard way when my entire Hetzner instance froze solid for six hours. The box only had 4GB of RAM, my Valheim server got ambitious, and the Linux Out-Of-Memory killer casually assassinated my Postgres container. I woke up to an inbox full of panicked monitoring alerts from my handful of beta users.
Just use Neon or Supabase free tiers for databases. A managed Postgres instance with 0.5GB of compute is absolutely $0 per month, it auto-scales to zero when it goes idle, and it takes the database failure completely off your personal周末 project plate. 
### How It Actually Flies
I haven't tested this specific Compose stack on ARM yet, so I can't vouch for the newer Hetzner Ampere instances. But on an x86 AMD instance, my deployment is strictly manual. Push to GitHub, SSH into the box, `git pull`, and run `docker compose up -d`. The whole process takes about 12 seconds.
You don't need a CI/CD pipeline or a zero-downtime blue-green deployment strategy for a side project that gets 40 visitors a day. 
The Reddit thread is right about one thing: getting all your hoarded trash into the air is deeply satisfying. Just skip the Nomad cluster. Skip the Kubernetes manifest. Stop hosting databases on $5 droplets. Put your hoard on one affordable box, throw a Traefik reverse proxy in front of it, and get back to actually building the features nobody asked for.
