---
title: "I Stopped Leaving My Self-Hosted Apps Running All Night"
date: 2026-08-01T07:54:57+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Stop paying Hetzner to idle Docker containers at 3 AM. Here is how I set up wake-on-request and cut my RAM usage in half."
---

I used to be a digital hoarder. I had a Hetzner CX21 box—4GB RAM, 2 vCPUs—running about 14 Docker containers around the clock. Paperless-ngx, Calibre-web, Actual Budget, three instances of Grafana for reasons I can no longer remember. 

Most of these apps existed to serve exactly one person. Me. And mostly on weekends.

I'd log in, check the dashboard, realize my PDF OCR pipeline was eating 1.2GB of RAM just sitting there idling, and close my laptop in disgust. Then I saw a thread on r/selfhosted that completely changed my approach. Someone complained about the ghost-town problem: paying €4.50 a month for a VPS that sleeps 22 hours a day. 

Someone else in the thread replied with a blunt question: "Why are you even leaving them running?" 

Good question.

### The Doctrine of Sleep

Most self-hosted apps don't need to be hot. If I only scan a receipt into Actual Budget on Sunday afternoons, keeping the Node.js container alive at 3 AM on Wednesday is pure waste. It consumes RAM, generates useless log rotations, and forces you to babysit updates for an app you aren't even looking at.

Inspired by the thread, I tested a brutal approach. I made an alias on my home server to just `docker stop actual-budget` when I was done. 

The results were immediate. Free RAM spiked. CPU temperature dropped. When I wanted to use it, I just typed `docker start actual-budget`. 

Naturally, I automated it. I wrote a tiny, ugly bash script that listens on a specific port, accepts an HTTP request, and fires off `docker start $CONTAINER`. I called it the "knocker." Hit the port, the app wakes up. Wait 30 minutes with no traffic, a cron job runs `docker stop`. 

I haven't tested this on ARM yet, and honestly, it probably requires a slightly different approach if you're running a Mac mini as a home server. But on my x86 Debian box, it's flawless. Your mileage may vary depending on how fast your storage can hydrate the container image. For me, the cold start is about 800 milliseconds. I can survive that.

### When It Fails

This setup is highly opinionated. It actively ruins RSS feed readers like FreshRSS. Throw a bot like Reeder at a powered-down instance and it just throws connection timeout errors. If your app relies on receiving external webhooks (like GitHub push events), it will definitely fail. You can't wake a container with a webhook if the container isn't running to receive it. You need a proxy in front to catch the hook and fire the wake command. 

I learned this the hard way. I put Immich behind a 30-minute idle timeout, only to find my phone app throwing a fit because it couldn't sync background uploads while I was out taking photos. 

Don't do this with your sync backends. Don't do this with Nextcloud if you use the mobile client. 

But for the web UI stuff you only touch from a laptop? Stop heating the room.

### What I Use Now

My home server currently runs about ten apps in hibernation. I run Docker, not Podman. The startup overhead difference is negligible here. 

I use Traefik as the reverse proxy. When I type `budget.mydomain.com` into my browser, Traefik hits the container's internal port. If it's down, it gives a 502 Bad Gateway. 

To fix that, I pointed Traefik at the knocker script instead. Web request comes in, the proxy hits the knocker, the knocker spawns the container, waits a second, and 302 redirects back to the actual app. It takes maybe one second. By the time the page actually renders, I've already forgotten I had to wait for a cold start. 

If your VPS provider charges you per egress gigabyte (like DigitalOcean, where outbound data gets pricey at $0.01/GB), you might think an idle app saves you money. It doesn't. It saves you bandwidth, but costs you RAM and CPU cycles. If you're running on unmetered Hetzner storage, the bandwidth costs are nil, but the RAM costs are eternal. 

Right now, my 4GB box sits at 12% memory usage at 2 AM. Paperless is asleep. Actual is asleep. Even my Fiftyone instance is down. 

I like my self-hosted stack a lot more when it isn't haunting me.