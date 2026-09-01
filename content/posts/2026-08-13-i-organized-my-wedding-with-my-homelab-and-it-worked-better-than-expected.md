---
title: I Ran My Wedding on a Homelab \u2014 Here's What Actually Broke
date: '2026-08-13T12:00:20+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding I Ran My Wedding on a Homelab \u2014 Here's What Actually Broke.
---

The post hit r/selfhosted on a Tuesday. Title was something like "I organized my wedding with my homelab." I clicked expecting a disaster. Instead, I found a guy who ran his entire wedding weekend off a Proxmox box in his closet. And honestly? It went better than most people's weddings go with a paid planner.
The thread is gold. Not because everything worked — because of what didn't.
## The setup was smarter than you'd think
OP ran a Dell PowerEdge T340 with 64GB RAM and a single 1TB NVMe. Nothing fancy. No clustered Kubernetes mess. Just Proxmox with three LXC containers:
- **Nextcloud** for shared docs and the guest list spreadsheet
- **Immich** for the photo album guests uploaded to during the reception
- **A simple Node.js app** for the RSVP site, behind Caddy
Total RAM usage: 11GB. Setup time: about four hours spread over a weekend. He said the hardest part was convincing his fiancée that "the server in the closet" wasn't going to crash mid-ceremony.
That's the real story here. Not the tech. The trust.
## What actually broke
Here's where it gets honest. The RSVP site handled 87 responses without breaking a sweat. Immich ingested 400+ photos from guests without a hiccup. The Nextcloud spreadsheet? Fine.
The printer died. The one running the seating chart. A $60 HP inkjet that had nothing to do with the homelab.
Also — and this is the comment that made me laugh — the domain registrar's DNS propagation took 40 minutes longer than expected. Someone in the thread called it: "You can self-host everything except DNS and your in-laws' opinions." That's the truest thing I've read on that subreddit all year.
## Why this matters now
Look, self-hosting your wedding is overkill. I'll say that plainly. For 99% of people, Google Forms and a shared album do the job. But that's not the point.
The point is that we've reached a moment where a normal person — not a sysadmin, not a DevOps engineer — can stand up a reliable stack in an afternoon. The tools got good enough. Nextcloud's mobile sync is genuinely solid now. Immich's face recognition is scary accurate. Caddy's automatic HTTPS means you don't touch certbot configs anymore.
The community thread had a guy running the same stack on a Raspberry Pi 4 with 4GB RAM. It worked. Slow, but it worked. Your mileage may vary on ARM, but the fact that this is even a conversation shows how far we've come.
## The one thing I'd push back on
OP used Docker Compose for everything. Fine. But a few commenters (myself included) would've gone Podman for the rootless containers. Docker's daemon running as root on a box that's about to host your wedding photos is a risk I wouldn't take. It's a small thing, but when the stakes are "my mom's crying because she can't see the photos from the first dance," you want every layer of isolation you can get.
Also — and this is the comment that got 200+ upvotes — he didn't have a backup. Not one. The entire wedding ran on a single NVMe with no replication. When someone asked "what if the drive dies at 3pm on Saturday?" his answer was "then we have a very analog wedding."
Bold. Stupid. But bold.
## The numbers that matter
- **Cost:** $0 in software. He already owned the hardware. A Hetzner CX22 VPS would've run him about $4/month if he'd gone cloud instead.
- **Uptime:** 100% across the entire weekend. Not a single service went down.
- **Time spent troubleshooting:** 15 minutes. All of it on the DNS issue.
- **Photos lost:** Zero.
Compare that to the $2,000 wedding website packages that exist. The ones that charge you monthly for a guest book feature. It's not even close.
## The real takeaway
You don't need a homelab to get married. You need one to remember that you *can* build things yourself. That's the feeling that thread captured. The quiet confidence of knowing your RSVP site is running on hardware you chose, software you configured, and a network you actually understand.
The printer still died though. Some things are beyond self-hosting.
### FAQ
**Is it actually cheaper to self-host a wedding website?**
Yes, if you already own the hardware. A used Dell or even a Raspberry Pi 5 ($80) plus a domain ($10/year) beats any paid wedding site after about six months. If you're starting from zero, a Hetzner CX22 at $4/month is still cheaper than most paid options and gives you full control.
**What's the minimum viable setup?**
One machine, Docker or Podman, Caddy for reverse proxying, and two containers: a static RSVP form (or Nextcloud for docs) and Immich for photos. Skip the database entirely if you can — a JSON file or SQLite handles 100 RSVPs fine. Expect 2-4 hours of setup time.
**Should I run this on a Raspberry Pi?**
It works, but I'd recommend against it for a wedding. The SD card corruption risk is real, and you don't want to explain to your new spouse why the guest book vanished. Use a proper SSD via USB or SATA, or just rent a cheap VPS. The peace of mind is worth the $4/month.
