---
title: How I Got My Homelab Ready for Upcoming SSL Certificate Changes
date: '2026-09-02 02:00:42+08:00'
draft: false
tags:
- selfhosted
- ssl
- linux
- homelab
summary: A step-by-step guide to tackling the upcoming SSL certificate expiration
  changes with tools, tips, and r/selfhosted wisdom.
---

Brace yourselves: there's an SSL problem brewing, and your homelab might be caught in the crossfire if you’re not ready. The general buzz around forums like r/selfhosted is about the new October 2026 Let’s Encrypt root cert expiration (DST Root CA X3). Most services patched this already, but for selfhosters, old clients, and niche apps, chaos could still strike.

Here’s how I prepped my homelab without descending into madness. Spoiler: this involves updating, checking dependencies, and watching logs like a hawk.

## Step 1: Check Your Current Certificate Chain

First, take stock. Start by analyzing the certificates your services are using. Use this one-liner with `openssl` on your server:

```bash
openssl s_client -connect yourdomain.com:443 -servername yourdomain.com
```

Look for the `Certificate chain` section. If it still shows the expired `DST Root CA X3`, you’ve got some updating to do. Someone on r/selfhosted mentioned they overlooked this step and spent hours debugging why an ancient iPhone couldn’t connect to their Jellyfin instance. Don’t be that person.

## Step 2: Update Clients and Dependencies

Do your apps even play nice with the new ISRG Root X1 cert? A surprising number of people in the comments confessed to running outdated clients. Some cert validation on older systems chokes on the new root cert, especially if it’s chained in legacy validation paths. Popular offenders: Python < 3.6, OpenSSL < 1.1.1, and Java 8.

Command to check your OpenSSL version:

```bash
openssl version
```

If you’re running something prehistoric like OpenSSL 1.0.x, upgrade it. On Ubuntu/Debian:

```bash
sudo apt update && sudo apt install --only-upgrade openssl
```

Running Docker containers? Yup, those need checking too. One poster flagged an issue with the official Ghost container bundled with an outdated CA cert store. If you're on dockerized apps, pull image updates:

```bash
docker pull ghost:latest
```

For Python projects (e.g., Home Assistant), tell `pip` to grab the latest `certifi` package:

```bash
pip install --upgrade certifi
```

## Step 3: Automate Renewal Checks (For Real This Time)

If you didn’t already automate your SSL renewals, now’s the time. Let’s Encrypt with Certbot is still the reigning king for selfhosters. Here’s my crontab for auto-renewals:

```bash
0 3 * * * /usr/bin/certbot renew --quiet --post-hook "docker restart nginx"
```

Notice that `--post-hook`. It restarts my reverse proxy (in this case, Nginx) after renewal, which saves you from the classic “new cert, old service connection” headache. Apache users, same concept:

```bash
0 3 * * * /usr/bin/certbot renew --quiet --post-hook "systemctl reload apache2"
```

If you’re using Caddy, congrats—you’re already living in the future. Its baked-in automation handles this without extra setup.

Optional sanity check:

```bash
certbot certificates
```

This confirms your renewal dates and lets you manually nuke any unused or old certs.

## Step 4: Monitor and Test Every Service

Don’t assume “it works on my Nginx” means your entire stack is fine. Run through every service that hits the public internet. Specific pain points people flagged in the thread:

- **Bitwarden**: Some setups use the bundled web server (`./bitwarden.sh updateself`) and need manual updates for the cert chain.
- **Pi-hole with DoH**: If you run DNS over HTTPS via Cloudflared, make sure your encrypted DNS client (or upstream resolver) handles the new root.
- **Legacy hardware**: Old Smart TVs couldn’t validate the new chain. r/selfhosted folks got around this by pinning ISRG Root certs directly.

Here’s a curl command I used to test my services headless:

```bash
curl -Iv https://my-service.example.com
```

Look for lines like `SSL certificate problem` or `unable to verify`. No news is good news.

---

## FAQ

### Why don’t my old devices trust Let’s Encrypt anymore?

A lot of legacy devices still rely on the expired DST Root CA X3. They aren’t getting updated trust stores because their vendors gave up. If you’re stuck with old gear, you can manually install the ISRG Root cert or use a reverse proxy to terminate SSL.

### Should I switch from Let’s Encrypt to ZeroSSL?

Some folks jumped ship after the DST Root CA X3 drama. ZeroSSL works fine and offers a longer 90-day renewal window, but their API-based workflow might demand more setup time. Unless you’ve had major issues, Let’s Encrypt is still the community default.

### I see “handshake failure” logs. Any ideas?

Common causes: outdated TLS versions (force TLS 1.2+), mismatched cert chains, or unpatched OpenSSL. Check your client and server logs. If it’s just one device misbehaving, it’s likely an untrusted root cert issue.

---

This is overkill for many, but if you’re running public-facing services or funky legacy hardware, it’s worth the prep. Comments are open—what’s your homelab SSL survival story?
