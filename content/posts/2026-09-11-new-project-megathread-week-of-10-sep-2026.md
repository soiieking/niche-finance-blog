---
title: How to Self-Host a RustDesk Server and Ditch TeamViewer
date: '2026-09-11 08:00:04+08:00'
draft: false
tags:
- selfhosted
- remote-desktop
- linux
- rustdesk
summary: Set up your own RustDesk server for remote desktop magic—faster than a coffee
  break, cheaper than a TeamViewer subscription.
---

## Why RustDesk?

Tired of TeamViewer crying about "commercial usage" because you connected to grandma's laptop one too many times? RustDesk gives you an open-source, fully self-hosted remote desktop solution minus the drama. It’s stupid simple to set up, performs well on most hardware, and has zero ongoing costs—except your VPS bill, if you're not running this at home.

This week on r/selfhosted, someone asked, “Can I ditch AnyDesk/TeamViewer for RustDesk, and how hard is it to host?” Spoiler: very possible. Let me show you how to get started.

---

## What You’ll Need

1. **A Linux machine**: Could be an Ubuntu VPS (Hetzner’s CX11 at $4/month works) or an old NUC collecting dust.  
2. **Docker**: Makes updates trivial. Podman works too, but I’m using Docker in this guide.  
3. **A domain (optional)**: Handy for pretty subdomains like `rustdesk.mydomain.com`.  

---

## Step 1: Fire Up the Server

The RustDesk server is split into two main components: the Rendezvous Server (`hbbs`) and the Relay Server (`hbbr`). The first handles "handshakes," while the second relays actual traffic between clients. Let’s get them running.

### 1. Install Docker and Docker Compose
```bash
sudo apt update && sudo apt install docker.io docker-compose -y
```

Done? Cool.

### 2. Create a `docker-compose.yml`

Head to your favorite CLI and make a directory for the deployment:  
```bash
mkdir ~/rustdesk-server && cd ~/rustdesk-server
nano docker-compose.yml
```

Now copy this in (from the r/selfhosted thread—props to user `linuxtinker`):

```yaml
version: "3"
services:
  hbbs:
    image: rustdesk/rustdesk-server:latest
    container_name: hbbs
    ports:
      - "21115:21115"    # Rendezvous server port
    restart: unless-stopped
  hbbr:
    image: rustdesk/rustdesk-server:latest
    container_name: hbbr
    ports:
      - "21116:21116"    # Relay server port
    restart: unless-stopped
```

### 3. Launch the Stack

Run:  
```bash
docker-compose up -d
```

And boom—your server backend is live.

---

## Step 2: Secure It (Optionally)

If you’re exposing this to the scary, scary internet:  
1. **Use a domain**: Point an A record to your server’s IP.  
2. **Set up a reverse proxy**: Nginx or Traefik works fine. Use Let's Encrypt for SSL.

For instance, if you’re running Nginx:  
```bash
sudo apt install nginx certbot python3-certbot-nginx
# basic Nginx conf for RustDesk:
nano /etc/nginx/sites-available/rustdesk.conf
```

Example config:  
```nginx
server {
    listen 80;
    server_name rustdesk.mydomain.com;

    location / {
        proxy_pass http://127.0.0.1:21115;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Switch to HTTPS:  
```bash
sudo ln -s /etc/nginx/sites-available/rustdesk.conf /etc/nginx/sites-enabled/
sudo certbot --nginx -d rustdesk.mydomain.com
sudo systemctl reload nginx
```

---

## Step 3: Point Your RustDesk Clients to Your Server

On the client side (Windows/Linux/Mac), open RustDesk and head to Settings. Under "ID/Relay Server," plug in your server’s domain or IP:  
- Rendezvous server: `yourdomain.com:21115`  
- Relay server: `yourdomain.com:21116`  

Test it. Connect to another device and watch RustDesk do its thing.

---

## What Works—and What Doesn’t

RustDesk is **blazing fast** for basic remote control tasks. File transfers? Seamless. Latency? Barely noticeable, even on modest hardware. It’s perfect for personal setups or small teams who want full control and minimal maintenance.

Caveats:  
1. No mobile support for self-hosted mode (as of v1.9.0). The Android app insists on RustDesk’s hosted relay.  
2. No AD/LDAP integration out of the box. For corporate types, this might be a dealbreaker.  

---

## FAQ

### Why can’t I connect?  
Double-check ports `21115` and `21116` are accessible (use `nc -zv`). Firewalls and NAT can cause headaches—don’t forget to poke holes.

---

FAQ JSON-LD:
```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why can’t I connect?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Double-check ports 21115 and 21116 are accessible (use nc -zv). Firewalls and NAT can cause headaches—don’t forget to poke holes."
      }
    }
  ]
}
