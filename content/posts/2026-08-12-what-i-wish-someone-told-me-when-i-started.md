---
title: What I Wish Someone Told Me When I Started Self-Hosting
date: '2026-08-12T22:00:16+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding What I Wish Someone Told Me When I Started Self-Hosting.
---

I've been running my own infrastructure for six years now. I've lost data, bricked servers at 2 AM, and once spent a full weekend debugging a DNS issue that turned out to be a typo in a `.env` file. The r/selfhosted community saved my sanity more times than I can count.
So I went back through the threads and pulled the best advice people actually give — not the marketing fluff, the real stuff.
## Your First Server Will Die. Plan For It.
The most upvoted comment in a recent "what do you wish you knew" thread came from u/ServerGremlin, who put it bluntly: *"Your first server is a learning machine, not a production box. Treat it like a disposable lab and you'll sleep better."*
They're right. My first Hetzner CX22 (€3.79/month, 2 vCPU, 4GB RAM) died during a routine `apt upgrade`. Kernel panic, unrecoverable. I had no backups. I lost three months of Jellyfin watch history and a Nextcloud instance I'd spent weeks configuring.
The fix isn't better hardware. It's better habits. Set up automated backups on day one, not day thirty. Use `restic` or `borgbackup` — both free, both dead simple. Point them at a separate storage bucket. I use Backblaze B2 at $6/TB/month, but Wasabi or even a second cheap VPS works fine.
## Docker Is Fine. Docker Compose Is Better. Kubernetes Is Overkill.
The community is genuinely split on containerization, but the consensus for beginners is loud: **start with Docker Compose, skip Kubernetes entirely.**
u/containers_are_life said it best: *"K8s is a distributed systems platform, not a homelab tool. You don't need a fleet orchestrator for a Pi and a mini PC."*
I've seen people spin up k3s clusters for three services. That's like buying a freight truck to move a couch. Docker Compose gives you declarative configs, easy rollbacks, and you can learn it in an afternoon. Podman is a fine alternative if you hate Docker's daemon, but honestly, the difference won't matter until you're running hundreds of containers.
One caveat: **pin your image versions.** I learned this the hard way when a `latest` tag pulled a breaking update and took down my entire stack. Use `image: linuxserver/jellyfin:latest` sparingly. Lock to specific tags for anything you actually depend on.
## The Hardware Rabbit Hole Is Real
You don't need a rack-mounted server with 128GB of RAM. You need something that runs 24/7 without melting your electricity bill.
u/pi_powerhouse summed it up: *"A Raspberry Pi 4 handles 90% of what people actually self-host. The other 10% is usually a database or transcoding, and you'll know when you hit it."*
My current setup: an old Dell OptiPlex with an i5-6500 and 16GB RAM I bought refurbished for $120. It idles at 15W and runs 20 containers without breaking a sweat. The Pi 4 (8GB, ~$75) is genuinely fine for most people — just don't expect it to transcode 4K video or run heavy databases.
If you need more power, look at used enterprise gear on eBay. A Dell PowerEdge T320 with 32GB RAM goes for under $200. But factor in the noise and power draw. My OptiPlex is silent; a rack server will sound like a jet engine in your living room.
## Networking Will Bite You In The Ass
Everyone thinks about storage and compute. Nobody thinks about networking until their reverse proxy stops working at 11 PM.
The single best piece of advice from the thread came from u/networking_nightmare: *"Learn what a reverse proxy does before you buy a domain. Caddy is the easiest, Nginx Proxy Manager is the most popular, and Traefik will make you cry."*
I use Caddy. It's one binary, automatic HTTPS, and the config is five lines. Nginx Proxy Manager has a nice UI but I've had it eat its own config on updates. Traefik is powerful but the learning curve is steep — I've seen grown adults rage-quit over its label syntax.
Also: **get a domain name.** A $10/year domain from Cloudflare beats remembering IP addresses and dealing with self-signed cert warnings. Cloudflare's free tier includes DNS, and their tunnel feature lets you expose services without opening ports — which is a whole security discussion on its own.
## Backups Are Boring Until They're Not
The thread had a recurring theme: everyone who lost data wishes they'd backed up sooner. u/data_hoarder_extraordinaire put it perfectly: *"Backups are like insurance. You hate paying for them until you need them, and then you'd pay anything."*
My rule now: 3-2-1 backup strategy. Three copies, two different media, one offsite. I run nightly `restic` backups to B2, weekly snapshots to a local USB drive, and I test restores quarterly. Testing restores is the part everyone skips — a backup you can't restore is just a collection of corrupted files.
## The Community Is The Real Resource
The best thing about self-hosting isn't the technology. It's the people. r/selfhosted has 500,000+ members who've already made every mistake you're about to make.
u/old_timer_selfhosted said: *"Ask questions before you break things, not after. The community is weirdly generous with their time if you show you've done your homework."*
Search before you post. Include your hardware specs and what you've already tried. Nobody wants to debug your setup when you haven't even checked the logs.
## Final Thoughts
Self-hosting is a hobby that rewards patience and punishes shortcuts. You'll break things. You'll lose data. You'll spend a Saturday fighting with YAML indentation. But when everything works — when you're streaming your own media, hosting your own files, and running services that would cost $50/month in SaaS fees — it's genuinely satisfying.
Start small. Back up early. Ask questions. And for the love of god, pin your Docker image versions.
### FAQ
**Do I need a static IP for self-hosting?**
No. A dynamic DNS service like DuckDNS or a Cloudflare tunnel handles this automatically. Most home ISPs change IPs infrequently, and the free tiers of these services are more than enough.
**What's the cheapest way to start?**
A Raspberry Pi 4 (8GB) at ~$75 plus a used hard drive. Total setup time: about two hours. You can run Nextcloud, Jellyfin, and a few other services comfortably.
**Is self-hosting actually cheaper than SaaS?**
Depends on what you're replacing. Hosting your own Nextcloud instead of Google Drive saves money long-term, but the real cost is your time. Expect to spend 5-10 hours per month on maintenance once you have a few services running.
