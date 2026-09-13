---
title: 'I Got Digitally Graffitied: A Self-Hosting Security Wakeup Call'
date: '2026-09-13 22:00:03+08:00'
draft: false
tags:
- selfhosted
- cybersecurity
- linux
- homelab
summary: A hard lesson on why lazy self-hosting security invites chaos—and how to
  avoid becoming a target.
---

## It Started with a Laugh, Then a Panic

So here's my recent internet horror story: someone digitally graffitied my public-facing service. Not ransomware, not a total nuke of my data—a simple, taunting "____ was here" page splashed across the web app I was proud of. Felt like I just left my door unlocked with a big sign saying *"Free Candy Inside."*

This started a rabbit hole of frustration, self-reflection, and troubleshooting I'll never forget.

### Where I Screwed Up

Context is key: this wasn’t my first rodeo. I’ve been self-hosting for years, first with Pi-hole and Nextcloud on a Raspberry Pi 3, then graduating to Proxmox and a dedicated Dell R720. So I *thought* my setup was decent. Turns out, mental security checklists don’t replace real ones. 

The root of my shame? Exposing an admin panel *without securing it properly.* Specifically, it was n8n (ver. 0.226.0) I’d spun up on Docker behind a basic Traefik reverse proxy.

But here’s the kicker: I had neglected to set up authentication for the n8n editor. Yup, the "admin panel" was sitting on port 5678, wide open like a Waffle House at 3 AM.

### Attackers Love Low-Hanging Fruit

Why would someone even bother to hit a random hobbyist’s self-hosted service? Answer: because they can. Public IPs with open ports get scanned *constantly*. There’s an entire economy based on sniffing these out, whether by accident (Shodan indexing) or intentioned (script kiddies running `masscan`). 

One long Reddit thread in r/selfhosted perfectly summarized it: *"You're not being targeted. You're just part of the noise."* These people didn’t care about me specifically—they probably found my exposed service in the middle of 10,000 other scans that night.

### How I Fixed It (and Rebuilt My Setup)

#### 1. **Kill the Exposure**
First, I scrambled to shut the damn thing down. Yanked the container, closed the ports, and triple-checked nothing else was leaking. Obviously, this is like patching up a wall after someone has already tagged it, but at least the graffiti would stop circulating.

#### 2. **Double Down on Authentication**
Out of sheer paranoia, I’ve started implementing authentication layers on everything. For n8n, this means pairing OAuth2 and a VPN-based access rule. Now **only devices on my local Tailscale network** can even know the admin panel exists.

#### 3. **Hardened the Reverse Proxy**
Traefik is great, but like every tool, it’s only as good as your implementation. Most of us in self-hosting land tack it onto Docker Compose and call it a day. Here’s the thing: if you’re using Traefik, *you need to leverage middlewares.* Rate-limiting middleware, forward-auth, and IP banning by CIDR are non-negotiables. Mine was basically naked by default.

For reference:
- [Traefik Middleware Docs](https://doc.traefik.io/traefik/middlewares/overview/)
- Rate limiting example: `--entryPoints.web.rateLimit.average: 100` (100 requests/minute baseline).

#### 4. **Move Logs and Alerts Into My Brain**
My logs were like the gym membership I paid for but never used. Monitoring isn’t optional. Forget syslog spam—you want real-time alerts. I landed on Uptime Kuma after trying similar setups like Zabbix and Grafana Loki. It’s lightweight and just enough for a homelab.

When Uptime Kuma pinged me that services weren’t responding, it gave me the "whoa something’s off" nudge before things escalated.

### Lessons Learned the Hard Way

The self-hosted community is full of lifesavers. Someone pointed me toward Cloudflare Tunnels in the aftermath, which makes sense if you want to ditch direct port exposure without caving to Big Cloud entirely. It’s a good middle ground between “DIY everything” and hiding behind NGROK or ngrok-like solutions.

Another key takeaway? **You don’t have to self-host *everything.*** Next time, instead of prioritizing the coolest tech stack, I’ll think more critically: does this need to be public-facing? Or can I just keep it internal?

"So what’s your budget, humility-wise?" That’s what someone asked me in the Reddit thread. Now, I get it. Mine was embarrassingly low.

### Final Thoughts

If you’re self-hosting, you’re already in the deep end of managing your own infrastructure. But if you don’t lock your doors—and I mean *actually* lock them—it’s just a vanity project with a bullseye on it. The internet does not discriminate between hobbyists and enterprises when it finds a poorly secured service. 

Were my tools bad? No. Was *I* lazy? Yep. It’s an easy trap to fall into, especially in the rush to just "get things working." I share this to say: be better than I was last week. Secure your stack before running to r/selfhosted to show it off.

---

### FAQs

#### What’s the quickest way to secure exposed services?
Start with a VPN like Tailscale or wire up Cloudflare Tunnels if you want less friction. For direct access, ensure strong passwords, rate limits, and basic firewalls like UFW.

#### Is Traefik overkill for small homelabs?
No, but it can be too permissive out of the box. If you're small-scale, alternatives like Caddy or Nginx Proxy Manager might be easier to secure from day one.

#### How often should I check my logs?
At least weekly if you’re busy, but set up alerts for anything public-facing. Tools like Uptime Kuma make this practical and not overwhelming.
