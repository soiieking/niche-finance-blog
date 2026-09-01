---
title: Best OS for a Selfhosted Server (I Broke All of Them So You Don't Have To)
date: '2026-08-12T10:00:15+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding Best OS for a Selfhosted Server (I Broke All of Them So You Don't
  Have To).
---

Look, this question gets asked every single week on r/selfhosted, and the comments are always a dumpster fire of tribal warfare. Debian purists screaming at Arch users. Someone inevitably recommends FreeBSD like they're revealing ancient wisdom. And there's always that one guy running everything on a Raspberry Pi with an SD card that's about to corrupt itself into oblivion.
I've been through this. Multiple times. My current homelab has survived three OS migrations, one accidental `rm -rf` (don't ask), and a power outage that taught me more about ZFS than I ever wanted to know. Here's the honest breakdown.
## The Baseline: Debian and Ubuntu
If you want to actually run services instead of babysitting your OS, Debian 12 is the answer. It's boring, it's stable, and it doesn't surprise you at 3 AM when your NAS decides to update itself.
Ubuntu Server 24.04 LTS gets the edge if you need newer packages or commercial support. But here's the thing — Snap is a nightmare. I've had Snap updates break Docker containers in ways that made me question my life choices. You can remove Snap entirely on 24.04, but why start with the headache?
The r/selfhosted thread had a guy running 47 containers on a Debian box with 8GB RAM and he hadn't touched it in 14 months. That's the energy you want.
## The "I Hate Systemd" Club: Alpine and Void
Alpine gets recommended a lot for Docker hosts because it sips RAM like a fancy cocktail — my minimal install idles around 120MB. But here's the catch: musl libc. Some precompiled binaries will just refuse to run, and you'll spend an evening compiling things that work in five minutes on Debian.
If you're running Kubernetes or plain Docker Swarm, Alpine's lightweight nature is genuinely compelling. For everything else? This is overkill for most people.
Void Linux is what I'd run if I wanted to feel cool at meetups. The runit init system is elegant, the package manager is fast, and everything just works. But the community is small. When something breaks, Google is suspiciously quiet about it.
## The Sane Alternative: Ubuntu Server
I know I just dunked on Snap, but hear me out. Ubuntu Server 24.04 LTS is what Hetzner gives you by default, and there's a reason for that — it's the path of least resistance. Almost every selfhosted tutorial assumes you're running it. Docker installs with three commands and works. Finding help for weird issues isn't a scavenger hunt.
Yeah, Canonical does some questionable things with their snap ecosystem. And yes, you should run `apt remove snapd` immediately after install — a 10-minute step that saves you months of weirdness.
## The Curious Case of NixOS
The thread had a guy describe NixOS as "declarative heroin" and honestly? He's not wrong. Your entire server config lives in one file. Rebuilds are atomic — if it breaks, you roll back in seconds. For reproducibility, nothing beats it.
But the learning curve is a brick wall. I spent three days writing a config that takes thirty minutes in Debian. The language is genuinely its own thing, and debugging package expressions will make you question whether you actually enjoy computers.
My verdict: use a Debian VM to learn NixOS before committing to it as a daily driver. It's a beautiful system that demands respect.
## What I Actually Run
My primary server (Hetzner CPX31, about $8/month) runs Debian 12 with Docker and Portainer. It's been up for so long I forget to renew my SSL certs until the browser complains — a problem I never had with other distros.
The next one (an old HP EliteDesk with 32GB RAM) runs Ubuntu Server for no good reason except that Proxmox comes installed as a package. Cheating, but convenient.
## FAQs
### Should I use a desktop distro like Mint or Fedora Workstation for my server?
No. Servers don't need GUIs. You're wasting RAM and attack surface for a desktop you'll never use. SSH into a headless install and thank me later.
### How much RAM do I actually need for a selfhosted server?
For a basic setup running Docker — a few containers like Heimdall, Jellyfin, and Vaultwarden — 4GB is comfortable, 8GB is better. My Alpine box runs 15 lightweight services in under 1GB.
### Why not use a NAS OS like TrueNAS or OpenMediaVault?
If you're building a storage appliance, TrueNAS Scale is fantastic. But if you want to run Plex AND a reverse proxy AND a game server, a plain Linux OS with Docker will give you more freedom without fighting the NAS overlay.
The thread's top comment summed it up best: "The best OS is the one you already know how to fix." For me, that's Debian. For you, it might be whatever your VPS provider's image library offers that you've used before. Just don't use CentOS — its EOL came and went, and there's no reason to start a new project on a graveyard. Your servers should outlive your patience, not the other way around.
