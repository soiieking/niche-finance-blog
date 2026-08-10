---
title: "I Scored the Homelab Lottery: A Free R710, 128GB RAM, and the Rabbit Hole That Followed"
date: 2026-08-11T00:00:08+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Free enterprise gear sounds amazing until you check the power bill. One r/selfhosted user's lottery win, and the hard truths about running old iron."
---

Somebody on r/selfhosted posted the dream scenario last week: a neighbor was clearing out an office and handed over a Dell R710. Not just any R710 — dual Xeon X5690s, 128GB of ECC RAM, and a couple of 10k SAS drives. Free. Ninety-nine percent of the comments were variations of "congrats" and "check your power bill."

That last part is the real punchline.

## The Free Server Tax

I've been down this road. Free hardware is never free. It's a down payment on electricity, noise, and heat. An R710 with dual X5690s idles around 150-180W. At the US average of $0.17/kWh, that's roughly $22 a month just to keep the thing breathing. Run it 24/7 and you're looking at $260+ a year before you even install Proxmox.

The thread's top comment nailed it: "Congrats, you now own a space heater that occasionally serves Jellyfin."

That's not hyperbole. My old R720 sounded like a 747 taking off until I swapped the stock fans for Noctua NF-F12s. That mod cost $60 and an afternoon of fiddling with IPMI fan curves. Worth it, but it's the kind of hidden cost nobody mentions in the "free server" celebration posts.

## What's Actually Worth Running on It

Here's where I'll push back on the naysayers. A free R710 with that much RAM is genuinely great for specific workloads:

- **Proxmox cluster node** — 128GB lets you run a dozen LXC containers without breaking a sweat. I've got Pi-hole, Vaultwarden, and a Minecraft server for the kids all coexisting on 16GB.
- **TrueNAS with ZFS** — ECC RAM is the killer feature here. You can run ZFS without the paranoia of silent corruption. This alone justifies the power draw for data hoarders.
- **Home Assistant with Frigate** — the Coral TPU does the heavy lifting, but the CPU headroom for object detection on multiple cameras is nice.

But if you're just running a few Docker containers? This is overkill. A used Dell OptiPlex Micro with an i5-8500T sips 15W and handles 90% of homelab use cases. You can grab one on eBay for $150 and it'll pay for itself in power savings within a year compared to the R710.

## The ARM Elephant in the Room

The community is genuinely split on this. Half the thread was "sell it and buy a Raspberry Pi 5" and the other half was "you'll pry my Xeon from my cold, dead hands."

I've tested both paths. The Pi 5 with 8GB runs Pi-hole, WireGuard, and a lightweight Gitea instance fine. But the moment you want to run a Postgres database with any real data, or transcode video with Jellyfin, it chokes. The R710 laughs at those workloads.

Your mileage may vary, but my rule of thumb: if you need more than 16GB of RAM or want to run VMs with actual isolation, old enterprise gear wins. If you're container-only and don't care about ECC, go ARM and save the planet.

## The Real Lottery Ticket

Here's the thing nobody in that thread said outright: the R710 is a trap if you treat it as your primary server. The X5690s are 14-year-old chips. They're slow by modern standards — a $200 Ryzen 5 5600G will beat them in single-threaded performance while using a fraction of the power.

The smart play is what one commenter suggested: use the free hardware to learn, then migrate to something efficient once you know what you actually need. I did exactly that. Ran Proxmox on the R720 for a year, broke everything repeatedly, learned ZFS and networking the hard way. Then I moved my critical services to a Hetzner CX22 ($4.50/month) and kept the R720 for batch jobs and experiments.

That's the actual lottery win. Not the hardware — the permission to break things without consequences.

## FAQ

### Is a free R710 worth it in 2026?

Only if you value learning over efficiency. The power draw will cost you $200-300/year. If that's acceptable as a tuition fee for learning Proxmox, ZFS, and enterprise networking, absolutely. If you just want services running, buy a mini PC instead.

### What's the best alternative to old enterprise gear?

Dell OptiPlex Micro or Lenovo ThinkCentre Tiny with an 8th-gen i5 or newer. They idle at 10-15W, support up to 64GB RAM, and cost $150-250 used. For Docker and light VMs, they're the sweet spot.

### Can I make an R710 quiet?

Yes, but it takes work. Replace the stock fans with Noctua NF-F12s and configure IPMI fan curves. Expect to spend $60-80 and a few hours. It'll still be louder than a desktop, but tolerable in a closet or garage.