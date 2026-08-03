---
title: "The 3 Stages of Self-Hosting: From Raspberry Pi to Digital Ocean to the Tin Foil Hat"
date: 2026-08-03T08:36:07+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Skip the obligatory 'I migrated from Google' Reddit post. Here is what actually happens when you fall down the self-hosting rabbit hole."
---

I saw a post on r/selfhosted last week that perfectly captured the delusion of this hobby. A guy was asking how to securely expose his proxmox cluster to the public internet. He was running Pfsense in a VM, had a single public IP, and was getting DDOSed. 

He was a Week 1 self-hoster trying to do Week 10 infrastructure. It never ends well. 

If you spend enough time in the trenches, you realize self-hosting isn't a switch you flip. It's a linear descent into madness. There are three distinct stages, and you cannot skip them without breaking your setup.

### Stage 1: The Pi and the Dashboard

In Stage 1, you just want to cut the cord on your photos. 

You buy a Raspberry Pi 4 with 4GB of RAM. You SSH into it, bash your head against read-only mount issues for three hours, and successfully install Docker. You deploy Immich. You restart the container.

It works. You feel like a god.

So you keep going. You install Nextcloud, because Google Drive costs $1.99 a month and you are going to beat Big Tech. You install Jellyfin, because you torrented 400GB of FLAC audio and need to stream it to your phone. You find a slick dashboard. 

Let's be clear: Dashboards are a Stage 1 mistake. 

Dashboards are a Stage 1 mistake. You spend an entire Saturday afternoon fighting Traefik or Caddy labels just to make the Homepage dashboard auto-discover your containers. It does not matter that you are the only person in your house who knows the dashboard exists. You want those green dots on a black background.

The problem with Stage 1 is the hardware. That Raspberry Pi is burning a hole in its own PCB. Once you have a 5GB photo library, Immich's background job processor will peg the ARM CPU at 100% for an hour just to extract faces from a single wedding album. The microSD card corrupts and dies from too many write cycles. 

You learn the hard way that SD cards are not real storage. 

### Stage 2: The Cloud VPS Exodus

Stage 2 begins when you realize your local internet is a joke.

I was running Bitwarden at home on an old Mini PC. My ISP gives me CGNAT. I tried a MagicDNS tunnel, but it felt hacky. So I migrated everything to the cloud.

The debate here is Hetzner vs DigitalOcean. I love DigitalOcean's UI. It is fast, clean, and responsive. But DO charges $24 a month for a 4GB droplet. Hetzner gives you a 4GB VPS for €4.59. At those prices, DO is for people with corporate expense accounts or zero budget awareness. 

So you buy the Hetzner box. But once you are on a public IP, you become a target. You fail your first SSH hardening. You think Fail2Ban is enough. It is not.

Before you know it, you are reading the `nginx` docs at 2 AM trying to figure out how to generate a valid wildcard cert without taking the whole stack down. 

Also, Docker networking becomes a waking nightmare. I haven't tested this on Podman, but Docker's internal DNS resolution randomly breaks if you put too many custom networks in a compose file. Last month I spent four hours troubleshooting a CORS error in Vaultwarden only to realize the Alpine container was resolving IPv6 and my VPS firewall was only blocking IPv4. 

You eventually get it stable. The VPS stays up for 300 days. You think you have arrived. You are wrong.

### Stage 3: The Bare Metal Tin Foil Hat

The final stage is pure paranoia.

You realize Hetzner is a datacenter. Datacenters spy on you. Or they go bankrupt. Or founders get arrested. Your data is on someone else's physical disk. 

You lurk on Craigslist. You find a Dell OptiPlex SFF with an i5 and 16GB of RAM for $75. You buy it. You install TrueNAS Scale. 

I love TrueNAS, but it has one fatal flaw: ZFS is a jealous god. You cannot just slap a random USB drive onto the system without breaking the storage pool config. It demands add-in HBA cards and SATA backplanes. 

You start buying 4TB refurbished enterprise drives off eBay for $40 each. You put them in RAIDZ1. The noise is deafening. The power bill goes up $15 a month. 

In Stage 3, you stop talking about apps. You start talking about TDP. You undervolt the CPU, which requires booting a custom GRUB entry in Debian that took you two tries to get right. 

You also become obsessed with backups. A VPS user relies on weekly snapshots. A Stage 3 self-hoster treats snapshots as disposable. You build a Proxmox Backup Server on old hardware with a 10TB ZFS pool. You write a bash script using `rclone` to push encrypted offline backups to Backblaze B2, because the datacenter is ephemeral. 

That rclone script takes 12 hours to sync. Your upload bandwidth is 35Mbps and Backblaze deduplication is weird on ZVol block storage. You do not care. You have achieved data sovereignty. 

### Where do you actually stop?

The community is genuinely split on this. Some people stay in Stage 1 forever. They just want the Plex server to work. There is no shame in that. I currently run a Stage 2 Hetzner box for public services and a Stage 3 local mini-ITX box for files. 

Just do not skip steps. If you jump straight to bare metal without understanding container networking, your security posture is Swiss cheese. And for the love of god, back up your keys. You will lose them eventually.