---
title: "I Broke My Own Homelab So You Don't Have To: A Security Testing Field Guide"
date: 2026-08-12T16:00:15+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Real-world security testing for self-hosted apps: what works, what's overkill, and the tools that actually caught vulnerabilities."
---

I spent last weekend trying to hack my own homelab. Not because I'm paranoid — okay, maybe a little paranoid — but because I got tired of reading "just use a reverse proxy and you're fine" on r/selfhosted. That advice is how people end up with their Radarr instance serving as a public SMB share.

Here's what actually happened when I stopped trusting and started verifying.

## The Baseline: You're Probably Not Getting Hacked, But...

Let's be real. The threat model for most homelabs is "random internet scanners" and "that one friend who knows your IP." Nation-state actors aren't targeting your Plex server. But I've seen enough posts from people who exposed Portainer to the internet and got crypto-mined within 48 hours to know basic hygiene matters.

The thread that inspired this had a comment from u/definitely_not_a_bot saying "I just run `nmap` once a month and call it good." That's like checking your car's oil by looking at the dipstick through the window. It's something, but it's not enough.

## What I Actually Did (In Order of Pain)

### Step 1: The Free Stuff First

I started with **Nuclei** (v3.3.4, if you care). It's a vulnerability scanner that runs templates against your exposed services. Setup took about 15 minutes on my Hetzner CX22 (€3.79/month, 2GB RAM — it handled it fine).

The output was humbling. It flagged my Grafana instance for a CVE I'd ignored for six months. Not because I'm lazy — because I didn't know it existed. That alone justified the setup time.

### Step 2: The Brutal Middle Ground

This is where I got opinionated. **OWASP ZAP** is the community darling, and I get it — it's free, it's powerful, and it'll find stuff. But running a full active scan against my own apps took 45 minutes per target and produced 200+ alerts, 95% of which were false positives or "informational" noise.

The community is genuinely split on this. Some people swear by ZAP's baseline scans in CI/CD. I think that's overkill for a homelab unless you're running something that handles money or medical data. For the rest of us, **Nikto** (v2.5.0) is faster and dumber — which is exactly what you want for a first pass. It caught a misconfigured CORS header on my API that ZAP missed entirely.

### Step 3: The One That Actually Hurt

I ran **Trivy** against my Docker images. This is the one that made me question my life choices.

My Immich container had 14 known vulnerabilities, including one critical in the base image. Not Immich's fault — the upstream image hadn't been rebuilt in three weeks. But here's the thing: I'd been running that image for two months. Trivy took 90 seconds to tell me what I should've known.

The fix wasn't sexy. I set up Watchtower with a `--schedule` cron job and pinned it to check daily at 3 AM. Cost me 20 minutes of setup and it's been silently fixing my mistakes ever since.

## The Tools I'm Keeping (And The One I'm Not)

**Keep:**
- **Nuclei** — the CVE discovery alone is worth it
- **Trivy** — because ignorance isn't bliss, it's a security hole
- **Fail2ban** — yes, it's basic, but it blocked 1,847 SSH attempts in my first week on a fresh VPS

**Ditch:**
- **OpenVAS** — I tried it on a 4GB RAM VPS and it nearly OOM-killed the box. The scan results were comprehensive but the setup time (2+ hours) and resource hunger make it a non-starter for most homelabs. Your mileage may vary if you've got a beefy Proxmox node.

## The Hard Truth Nobody Wants to Hear

Security testing isn't a one-time thing. It's a habit. I've got a cron job that runs Nuclei and Trivy every Sunday at 6 AM, dumps results to a file, and emails me if anything's critical. Total setup time: 30 minutes. Total ongoing effort: zero.

The alternative is what I did before: assuming my Docker Compose stack was fine because I used strong passwords and didn't expose port 22. That's the self-hosted equivalent of locking your front door but leaving the window open.

## FAQ

**Q: Do I need to do all this if I only access my apps via Tailscale or WireGuard?**

A: No. If your services aren't exposed to the internet, your attack surface shrinks dramatically. But run Trivy anyway — compromised base images can still bite you from the inside.

**Q: Is there a one-command solution?**

A: Not a good one. I've seen scripts that chain nmap + Nikto + Nuclei, but they produce so much noise you'll ignore the output. Better to run targeted scans and actually read the results.

**Q: What about hosted scanners like Detectify or HackerOne?**

A: Overkill for a homelab. Those are for companies with compliance requirements. You're paying $100+/month for reports you'll skim once. Stick with the free tools.

---

*I haven't tested any of this on ARM (Raspberry Pi users, let me know if Trivy runs smoothly for you). And if you're running Kubernetes at home, you're beyond my help — that's a whole different beast.*