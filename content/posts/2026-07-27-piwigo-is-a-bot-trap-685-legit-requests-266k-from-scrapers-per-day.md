---
title: "Is Piwigo a Bot Trap? Surviving 266k Daily Scraper Requests on Self-Hosted Media"
date: 2026-07-27T22:02:08+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Self-hosting Piwigo? Discover why scrapers might hammer your server with 266k daily requests and learn how to block them using Nginx, Fail2Ban, and Cloudflare."
---

## The Community Spark: The Piwigo Bot Epidemic

A recent bombshell post on the `r/selfhosted` subreddit titled *"Piwigo is a bot trap 💀 685 legit requests, 266k from scrapers PER DAY"* sent shockwaves through the community. A user exposed their server logs, revealing a staggering 99.7% of inbound traffic was malicious scraper bots. 

Why is Piwigo specifically targeted? As an open-source photo gallery, it presents a treasure trove of metadata, image URLs, and bandwidth for AI training scrapers and malicious mirroring bots. The community quickly rallied to diagnose the issue and share battle-tested defenses.

## Synthesized Community Perspectives

The `r/selfhosted` consensus landed on a hard truth: **leaving a media gallery exposed to the public internet is an invitation to be scraped.** 

### The Root Cause盲点
Users noted that Piwigo’s default configuration is highly permissive. It returns large XML/RSS feeds and exposes EXIF data without rate limits. Community members debated the culprit: some blamed aggressive AI training crawlers (like GPTBot and ClaudeBot), while others pointed the finger at legacy image scrapers building unauthorized stock photo databases.

### The "Cloudflare vs. Local Defense" Debate
A major split emerged between two philosophies:
1. **The CDN Defenders:** Argued that routing traffic through Cloudflare is the only sane way to survive modern scraping volume.
2. **The Purist Sysadmins:** Argued that relying on third-party CDNs violates the ethos of self-hosting, preferring hardened local Nginx rate-limiting and WAF rules to maintain absolute server control.

## Deep-Dive Actionable Guide: Fortifying Piwigo

To protect your Piwigo instance and reclaim your server resources, implement this multi-layered defense strategy synthesized from the community's frontline experience.

### 1. Restrict Bots at the Reverse Proxy (Nginx)
Block known AI scrapers and aggressive crawlers directly in your Nginx server block before they even hit PHP.

```nginx
# /etc/nginx/conf.d/piwigo-bots.conf

map $http_user_agent $bad_bot {
    default 0;
    "~*GPTBot" 1;
    "~*ClaudeBot" 1;
    "~*Bytespider" 1;
    "~*SemrushBot" 1;
    "~*AhrefsBot" 1;
}

# Inside your Piwigo server block
server {
    location / {
        if ($bad_bot) {
            return 403;
        }
    }
}
```

### 2. Implement Nginx Rate Limiting
Protect your Piwigo endpoints from brute-force scraping. Add this to your `nginx.conf`:

```nginx
http {
    # Define a zone allowing 5 requests per second per IP
    limit_req_zone $binary_remote_addr zone=piwigo_limit:10m rate=5r/s;
}
```
Apply it to your Piwigo `location` block:
```nginx
location / {
    limit_req zone=piwigo_limit burst=10 nodelay;
    proxy_pass http://127.0.0.1:8080; # Assuming Piwigo runs on port 8080
}
```

### 3. Lock Down Piwigo's Internal Settings
Inside your Piwigo admin dashboard:
- Navigate to *Configuration > Options > General* and disable guest access if you don't need a public gallery.
- Turn off RSS feeds (*Configuration > Options > Notification*) as these are prime targets for automated scrapers.
- Strip EXIF data on upload to make your images less valuable to data harvesters.

## Comparing Defense Solutions

| Solution | Pros | Cons | Best For |
| :--- | :--- | :--- | :--- |
| **Cloudflare Proxy** | Stops 90% of bots at the edge, saves VPS bandwidth, free tier | Requires moving DNS, potential "challenge" loops for legit users | Admins wanting a "set and forget" fix |
| **Nginx Rate Limiting** | Keeps server control local, highly customizable | Requires manual tuning, consumes VPS CPU during spikes | Purist self-hosters |
| **Fail2Ban + CrowdSec** | Automatically bans IPs based on 403/404 error logs | High memory usage for massive IP tables, reactive | Servers under targeted attacks |

## The Verdict: Expert Advice

If you are self-hosting Piwigo in 2026, you cannot rely on default configurations. 

- **For Public Galleries:** You must use a CDN like Cloudflare. Configure WAF rules to block empty user agents and challenge known scraper ASNs. It is the only way to survive 200k+ requests a day without crashing your VPS.
- **For Private/Family Galleries:** Skip the CDN. Put Piwigo behind your reverse proxy, enforce Basic Authentication at the Nginx level, and completely disable guest access in Piwigo.

## Frequently Asked Questions (FAQ)

**Why does Piwigo attract so many scrapers?**
Piwigo generates predictable HTML structures, RSS feeds, and exposes EXIF metadata. These elements are highly valuable to AI training crawlers and bulk image mirroring bots.

**Is 266k requests per day normal for a self-hosted site?**
It is increasingly common in 2026 for any exposed web asset. Bots run continuously, probing for vulnerabilities and scraping data. 99% of self-hosted traffic being bots is a standard reality without proper filtering.

**Does blocking GPTBot stop all AI scrapers?**
No. You must block GPTBot, ClaudeBot, Bytespider, and CommonsBot. Additionally, many malicious scrapers spoof user agents, so you must combine UA blocking with rate limits or CAPTCHAs.

**Will Fail2Ban completely stop Piwigo scrapers?**
Fail2Ban is highly effective for banning IPs that repeatedly hit 403 forbidden pages, but for extremely distributed botnets, Cloudflare or CrowdSec is recommended to handle the volume efficiently.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why does Piwigo attract so many scrapers?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Piwigo generates predictable HTML structures, RSS feeds, and exposes EXIF metadata. These elements are highly valuable to AI training crawlers and bulk image mirroring bots."
      }
    },
    {
      "@type": "Question",
      "name": "Is 266k requests per day normal for a self-hosted site?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "It is increasingly common in 2026 for any exposed web asset. Bots run continuously, probing for vulnerabilities and scraping data. 99% of self-hosted traffic being bots is a standard reality without proper filtering."
      }
    },
    {
      "@type": "Question",
      "name": "Does blocking GPTBot stop all AI scrapers?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No. You must block GPTBot, ClaudeBot, Bytespider, and CommonsBot. Additionally, many malicious scrapers spoof user agents, so you must combine UA blocking with rate limits or CAPTCHAs."
      }
    },
    {
      "@type": "Question",
      "name": "Will Fail2Ban completely stop Piwigo scrapers?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Fail2Ban is highly effective for banning IPs that repeatedly hit 403 forbidden pages, but for extremely distributed botnets, Cloudflare or CrowdSec is recommended to handle the volume efficiently."
      }
    }
  ]
}
</script>