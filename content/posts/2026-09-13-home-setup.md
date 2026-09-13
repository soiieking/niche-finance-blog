---
title: The Real Cost of Self-Hosting at Home vs Using a VPS
date: '2026-09-13 10:00:02+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- homelab
summary: 'Breaking down hardware, electricity, and convenience: is self-hosting at
  home still worth it compared to renting a VPS?'
---

## Why Every Self-Hosted Project Hits This Question

At some point, whether you’re spinning up a Nextcloud instance or running a personal wiki, you’re going to ask: “Should I keep this at home or move it to the cloud?”

This isn’t just about convenience. It’s about hard costs (hardware, power), soft costs (your time), and the lurking reality that your ISP still doesn’t care about you. The answer depends not only on your needs but on market trends that keep changing. Let’s break it down.

---

## Hardware: Resurrect the Old PC or Pony Up for a NUC?  

First, let’s address the most common “home setup” route: repurposing old hardware. You’ve seen it on the subreddit—“Dig out your old laptop and call it a day.” This works great for getting started with low-demand containerized apps like Pi-hole or Tailscale. But for anything CPU- or I/O-intensive, you’re in the weeds pretty fast.

A good beginner box: a low-powered Intel NUC or Raspberry Pi 4. 

- New-gen Raspberry Pi 4B (8GB) costs ~$75 if you’re lucky and hate scalpers. Pulls ~3-4W idle, which is pennies a day on your power bill.  
- Intel NUCs (like the 10th gen Core i3 model) hover around ~$300, idle at 10W. These are fun but absolute overkill for Pi-hole-level jobs. Save them for Plex transcoding or something with ZFS.  

Both are whisper-quiet and sip energy, but here’s the problem: server-grade redundancy is expensive. You can cobble together RAID with used parts, but now you’re juggling more hardware and higher failure points. Plus, backups need to live somewhere else, which means external drives or another off-site box like a rented VPS.

For serious home setups (24/7 uptime, >4 apps), your entry price is closer to $500-$700 upfront (NUC + SSDs + random accessories).

---

## Power Addicts Anonymous: Electricity Costs Matter Now

Let’s talk power bills. This is the big, boring elephant in the room. In 2021, you could get away with 24/7 home setups because electricity prices hadn’t exploded everywhere. Now? 

A typical 10-15W device idling 24/7 eats ~11 kWh/month. At $0.15 per kWh (US average), that’s about $1.65/month. No big deal. But scale that to a beefy setup—like a retired Dell R720 (~100-150W idle)—and you’re closer to 100 kWh/month, or $15 just to sit there doing nothing even when nobody’s streaming Plex.

European friends? Double those numbers. An r/selfhosted user in Norway recently broke down their home server setup: 180W idle, costing ~$40/month just for power. That’s more expensive than a decently spec’d DigitalOcean droplet.

This doesn't mean "don’t self-host," but power costs are real. And if your setup grows—say, by adding GPUs for AI workflows—they’re going to become unavoidable.

---

## The ISP Bottleneck: An Upload Desert

Here’s the part that should bother you more than any one-time hardware spend: residential internet sucks. Most of us can still get away with asymmetric bandwidth (e.g., 1 Gbps download / 35 Mbps upload) for simple tasks like a daily WireGuard VPN, but hosting media for family across the country? Forget it. You need upload speed they won’t throttle—and guess what? ISPs are too busy selling Netflix bundles to care.

This is where VPSs win. Even a $5/month Lightsail instance gets you ~100 Mbps symmetric by default. Most home ISPs can’t match that unless you shell out for fiber, which is still a pipe dream in most suburban markets.

If you’re reading this and yelling “but Starlink!”—nope. Starlink’s a latency demon (good luck self-hosting gaming servers) and the data caps get dicey.

---

## When Does Renting a VPS Make More Sense?

Cloud providers like Hetzner and DigitalOcean are stupid cheap these days. Hetzner particularly shines for hobbyists: €4/mo (~$4.30 USD) gets you 2 CPUs, 20GB NVMe, and 2TB transfer. Horrible for Plex, but fantastic for 99% of web apps and microservices.

Running a VPS also transfers problems from your living room to someone else’s data center. Their hardware fails? They fix it. Your local setup dies during a power surge? Good luck finding spare parts for that RAID ten years later.

The downside? You don’t have full physical control (technically, they do). And some things are hard (or expensive) to self-host off-prem, like a full-on NAS or GPU-heavy ML workflows.

---

## TL;DR: It’s Not Either/Or  

Self-hosting at home is all about trade-offs. If you like tinkering, already own decent hardware, and have reliable (and cheap) power, go for it. Small apps and services like Nextcloud, Jellyfin, or even Home Assistant can thrive on a Raspberry Pi or old laptop.  

But if you’re hitting ISP limits or scaling apps, roll your wallet at Hetzner, Linode, or Oracle’s free-tier. A VPS shifts the burden of uptime and bandwidth management for a pittance—and that bandwidth alone can’t be beaten if your home upload sucks.  

One other option: hybrid setups. Run your "move fast, break things" experiments locally, then deploy the stuff that matters (public-facing web apps) to the cloud. Best of both worlds. Just don’t get so deep in the weeds that you forget *why* you wanted control in the first place.

---

### FAQs

#### **Can I mix local hosting with a VPS?**
Yes. Many folks use a VPN (like Tailscale or ZeroTier) to securely bridge local devices to a VPS. You get cheap bandwidth from the VPS while keeping private data “at home.”

#### **Are ARM devices like Raspberry Pi’s worth it?**
For lightweight apps? Absolutely. Just remember they max out fast. Once you’re running more than 2-3 Docker containers, even the 8GB Pi starts feeling constrained, especially with I/O.

#### **What’s better for Plex or media hosting?**
Home setups win hands down. Media servers chew through bandwidth and require big storage. If you’re serving your own movie library, buy HDDs + a decent home NAS.

---
