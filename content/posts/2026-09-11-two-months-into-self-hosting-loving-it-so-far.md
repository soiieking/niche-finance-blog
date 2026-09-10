---
title: 'Two Months of Self-Hosting: What I Learned and Why I’m Hooked'
date: '2026-09-11 00:00:03+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: Two months into self-hosting everything from media servers to backups. Wins,
  fails, and the ugly in between.
---

## How It Started: Overkill, Naturally

Two months ago, I fell into the self-hosting rabbit hole. What kicked it off? I wanted a simple Jellyfin server for my family. What happened? I spiraled into running Home Assistant, Nextcloud, and a VPN just because I could.

I started small: a Hetzner CX21 VPS (€4.85/month at the time) loaded with Docker. Why Hetzner? It’s cheap compared to DigitalOcean, doesn’t skimp on performance, and their traffic limits are stupidly generous (20TB per month, lol). Plus, their UI doesn’t try to sell me Kubernetes every five seconds.

Spoiler: that VPS got crowded fast. Which brings me to my first lesson…

## Lesson 1: RAM Matters More Than You Think

Docker is nice until you realize every container stacks overhead. Even with slim images, my 2 GB VPS was struggling after a week. Jellyfin alone, indexing all my 4K media, ate ~1.2 GB during transcoding. And yeah, *swap happened.* Watching your server crawl to death mid-movie? Not ideal.

I upgraded to a CX31 (4 GB RAM, €9.70/month). Problem solved. My advice? If you plan to run memory-heavy stuff like media servers or AI bots, go for at least 4 GB from the beginning.

## What Works, What Sucks

### Stuff I Love

1. **Jellyfin**: Absolute champ. Free, no BS, and looks great. I tried Plex as a control test, but then I saw the “Sign in with Google” nonsense on their homepage. Nope.
   - Bonus: Jellyfin’s performance is surprisingly good on Hetzner. I streamed a 4K HDR movie to my Fire TV Stick without hiccups.

2. **WireGuard**: Dead simple VPN. Took me 10 minutes to set up via the awesome [wg-easy](https://github.com/WeeJeWel/wg-easy). Now I’m my own VPN provider, which is way cooler than paying Surfshark.

3. **Home Assistant**: Super fun once you stop pulling your hair out. I’m running this on a Raspberry Pi 4 at home instead of the VPS because latency adds up when flipping smart light switches. Pro tip: the ZHA integration for Zigbee? Life-changing.

### Stuff That Almost Made Me Quit

1. **Nextcloud**: I wanted to self-host my files. Simple concept, right? NOPE. The Nextcloud database kept acting up (MariaDB v11, BTW). And every update feels break-prone. If you don’t ABSOLUTELY need the Nextcloud calendar or collaborative tools, just run Syncthing instead. It’s lightweight and *doesn’t* make you babysit SQL.

2. **Reverse Proxies**: Probably spent 50% of my setup time just swearing at Traefik. It’s powerful but overkill for simple homelab use. I switched to Nginx Proxy Manager, and now I barely touch it unless I need to renew a wildcard SSL.

## Security Paranoia (You Need It)

Let me put this bluntly: *your self-hosted server will get probed 24/7*. Within hours, my VPS logs showed random IPs hammering SSH. The solution was simple:
- Close ports. Only open what you need.
- Enable WireGuard and SSH key-based logins. DO NOT allow password logins unless you’re into giving hackers free VPS access.
- And yeah, don’t forget fail2ban.

I followed the advice of an r/selfhosted commenter who said, “If you don’t enjoy configuring UFW, self-hosting isn’t for you.” Spot on.

## Is It Worth the Headache?

Absolutely. But there’s a catch.

Self-hosting is like owning a pet. It’s rewarding, but you can’t just ignore it. Servers need updates, backups (shoutout to Restic for keeping me sane), and occasional troubleshooting. If you’re more of a set-it-and-forget-it type, maybe stick with cloud services.

Still, there’s something deeply satisfying about owning my stack. Google losing access to my files? Gmail mining my emails? Can’t relate.

## Final Thoughts

If you’re thinking about self-hosting, start small. Don’t try to run 10 services on day one. Build confidence with one stack—say, Jellyfin and WireGuard—and grow from there. Also, overprovision your RAM. I know I already said this, but it saved me from rage-quitting more than once.

Am I 100% self-hosted yet? Nope, and probably never will be. I’m not self-hosting email (Postfix warrants its own therapy group), and I still use Google Photos. But for my core stuff—VPN, media, home automation—I’m in, flaws and all.

No regrets. Well, maybe some for Nextcloud.
