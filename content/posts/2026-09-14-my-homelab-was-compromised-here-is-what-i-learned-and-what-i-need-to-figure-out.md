---
title: 'My Homelab Was Hacked: What I Learned (and What I Still Don’t Know)'
date: '2026-09-14 14:00:05+08:00'
draft: false
tags:
- selfhosted
- cybersecurity
- homelab
- linux
summary: My homelab got owned. Here's what happened, why it hurt, and how I'm rethinking
  my self-hosting setup to avoid it happening again.
---

## How It Started: My Stupid Mistakes

I’ll spare you the fake drama. My homelab was compromised, and it was 100% my own fault. Someone got in, started mining Monero on my server, and nuked a bunch of my containers in the process. Yeah, it's cliché — but apparently it’s still profitable.

The cause? I left an exposed RDP port open on the WAN while testing something (stupid). It was supposed to be temporary, but "temporary" turned into weeks because life got busy. Couple that with a weak password (no, seriously — *I* should know better), and it was basically an engraved invitation.

Lesson one: Anything you expose to the internet is under attack *constantly*. If you think “nobody will notice,” you’re wrong. I checked the logs post-mortem, and the attack started less than 24 hours after the port was opened. Bots don’t sleep.

## The Aftermath: When Procrastination Hurts

Here’s what sucked the most: I hadn’t been keeping proper backups. Yeah, I know. **Backups are the golden rule of self-hosting.** You don’t even need Reddit to tell you that. But my setup relied heavily on Docker containers and bind mount volumes, and I’d never gotten around to automating snapshotting.

So when the attacker wiped the volumes (guess they wanted to cover their tracks?), I lost *everything*. Personal media server configs, a half-written Mastodon instance, two VMs running testing environments for work projects — poof. Gone.

Lesson two: Backups aren't optional. Use something like [Duplicati](https://www.duplicati.com/) or [Restic](https://restic.net/) to sync critical data offsite. If you’re scaling up, I hear good things about [borgbackup](https://www.borgbackup.org/) too. Just budget some time for it—seriously.

## What I Fixed: Immediate Changes

### Locking Down Ports

The first thing I did was set up proper firewall rules with UFW. Default deny, allow by exception. You’d think this would’ve been obvious, but… well, hindsight. I also went all-in on WireGuard. You don’t really need 17 services exposed to the wild; you need *one* secure VPN entry point.

Also, screw RDP. Never again.

### Changing Auth

I swapped out weak passwords for SSH keys across the board. Public key auth isn’t perfect — it’s a hassle if you regularly reimage your devices — but it’s a giant step up from passwords. Combined with fail2ban, it’s enough to keep scripts and low-tier attackers out.

For web-based services, I set up Authelia as a reverse proxy authentication layer. It’s overkill for most people (don’t add complexity just because it’s neat), but I like having a centralized 2FA setup for things like Home Assistant and Jellyfin.

### Implementing Backups (Finally)

Right now, I’m running Duplicati with encrypted backups to a Hetzner storage box. About €4 a month for a terabyte, and it just works. There are cheaper options if you want to self-host (MinIO, if you have the bandwidth and patience), but personally, I’m fine with Hetzner eating my storage headaches.

Backups run every night, and I’ve already tested restoring them on a fresh VM. You’re not "done" with backups until you *test* them, by the way — something I learned the hard way.

## What I Still Don’t Know

### How Far Did They Get?

One thing I haven’t fully figured out: what else they compromised. The miner was obvious, but did they try lateral movement? Did they scrape any sensitive API keys or credentials from my containers? It’s hard to say without forensic tools I don’t have. I’ve wiped all the machines to be safe, but there’s definitely a paranoid part of me that wonders if I’m still missing something.

### Should I Be Using a VPS Instead?

Homelabbing is half the fun of self-hosting, but I’m starting to think some of my more critical services (e.g., Nextcloud for work docs) might be happier on a managed VPS. Hetzner, Linode, DigitalOcean — $5/month for peace of mind is hard to argue with. Especially if you want reliable network-level DDoS protection. That said, the DIY spirit is real, and this community tends to hate the "cloud vs self-hosted" debate with good reason.

## Final Thoughts: Self-Hosting Isn’t Set-and-Forget

It’s easy to let your homelab become a playground where nothing feels serious. But once you’ve got even a single useful service running, you’ve also got responsibility. Stuff like firewalls, SSH policies, and backups matter, because the internet doesn’t care whether your server is a hobby or a business. Either you harden it now, or some bot will find you — and trust me, they won’t send flowers.

Next steps for me: more threat modeling, maybe migrating a few things to a VPS, and getting better at monitoring. Also, closing ports when I’m done testing. That should’ve been obvious, yeah?

---
