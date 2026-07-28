---
title: "Super Productivity v18.16.0 Released: Why r/selfhosted is Buzzing About This Update"
date: 2026-07-29T00:28:18+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Super Productivity v18.16.0 brings major sync stability fixes and Nextcloud integration improvements. Here's what the r/selfhosted community thinks and how to deploy it securely."
---

## The Community Spark: Why r/selfhosted is Buzzing

Recently, a post on `r/selfhosted` highlighting the release of **Super Productivity v18.16.0** gained significant traction. The open-source, privacy-focused To-Do and time-tracking app has long been a favorite for users wanting to escape the prying eyes of proprietary SaaS tools. However, previous versions faced intermittent sync conflicts when self-hosted via Nextcloud or WebDAV. 

The community thread wasn't just a release announcement; it was a behind-the-scenes troubleshooting session. Users shared their lived experiences with database bloat, mobile battery drain, and reverse proxy configurations. This post synthesizes those real-world deployments into a definitive update guide.

## Synthesized Community Perspectives

Digging through the `r/selfhosted` thread, a strong consensus emerged around three core changes in v18.16.0:

1. **Nextcloud Sync Stability:** Users with multi-device setups (Desktop + Android) reported that v18.16.0 finally resolves the "UUID mismatch" sync conflicts that plagued earlier v18 releases. 
2. **Markdown Export Fidelity:** A reintroduced feature allows flawless exporting of project notes to Markdown, which users praised for integration into Obsidian and Logseq vaults.
3. **The Docker Image Size Debate:** While the core app is leaner, some community members debated the new bundled Docker image. "Why is the image pulling Node 20 when it runs perfectly fine on a minimal Alpine base?" one user noted. The consensus? Use the official Alpine-tagged image for VPS deployments to save resources.

## Deep-Dive Actionable Guide: Deploying v18.16.0 on a VPS

If you are upgrading or deploying Super Productivity for the first time, following the community-tested Docker Compose method ensures stability and easy reverse proxying.

### Step 1: Update your Docker Compose Configuration

Create a `docker-compose.yml` file. We recommend the Alpine variant to keep your VPS footprint minimal, as validated by power users in the community.

```yaml
version: '3.8'
services:
  super-productivity:
    image: johannesjo/super-productivity:latest-alpine
    container_name: super_productivity
    restart: unless-stopped
    ports:
      - "8080:80" # Maps internal web server to host port 8080
    volumes:
      - ./sp-data:/app/data
    environment:
      - TZ=America/New_York
```

### Step 2: Deploy and Reverse Proxy

To securely expose the app without exposing port 8080 directly, use Nginx as a reverse proxy. Launch the container, then set up your Nginx block:

```bash
docker compose up -d
```

```nginx
server {
    listen 80;
    server_name productivity.yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Run `sudo certbot --nginx -d productivity.yourdomain.com` to secure it with Let's Encrypt.

## Pros & Cons of the v18.16.0 Update

| Feature | Pros (Community Consensus) | Cons / Limitations |
| :--- | :--- | :--- |
| **Nextcloud Sync** | Eliminates previous UUID conflict loops; seamless multi-device. | Requires Nextcloud WebDAV endpoint to be explicitly definedForObject storage sync. |
| **Markdown Export** | Perfect formatting for second-brain apps (Obsidian). | Lacks automated scheduled exports; requires manual trigger. |
| **Docker Deployment** | Alpine image is incredibly lightweight (~120MB). | Initial cold start takes slightly longer than previous versions. |

## The Verdict: Expert Advice

Based on the `r/selfhosted` discussions and technical analysis of the release, **v18.16.0 is a must-upgrade**. 

If you are a **single-device user**, the markdown export feature alone makes the upgrade worthwhile for backups. If you are a **multi-device, self-hosted user** relying on Nextcloud, this update is critical for data integrity. The dev team has clearly listened to the bug reports from the community.

## Frequently Asked Questions (FAQ)

**Is Super Productivity v18.16.0 free to use for commercial teams?**
Yes. Super Productivity is licensed under the MIT License, making it entirely free and open-source for both personal and commercial self-hosted deployments.

**Can I sync Super Productivity without Nextcloud?**
Yes. The app supports generic WebDAV sync. You can point the sync settings to any WebDAV-compatible server, including Synology Drive or a simple Apache WebDAV directory.

**Does the Android app support the new v18.16.0 sync fixes?**
Yes, the F-Droid and Google Play versions have been updated simultaneously to support the new sync protocol, resolving mobile-specific battery drain caused by infinite sync loops.

**How do I backup my Super Productivity data before upgrading?**
Navigate to Settings > Data > Export. Choose the JSON or new Markdown format to save a local backup to your machine before pulling the new Docker image.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Is Super Productivity v18.16.0 free to use for commercial teams?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. Super Productivity is licensed under the MIT License, making it entirely free and open-source for both personal and commercial self-hosted deployments."
      }
    },
    {
      "@type": "Question",
      "name": "Can I sync Super Productivity without Nextcloud?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. The app supports generic WebDAV sync. You can point the sync settings to any WebDAV-compatible server, including Synology Drive or a simple Apache WebDAV directory."
      }
    },
    {
      "@type": "Question",
      "name": "Does the Android app support the new v18.16.0 sync fixes?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, the F-Droid and Google Play versions have been updated simultaneously to support the new sync protocol, resolving mobile-specific battery drain caused by infinite sync loops."
      }
    },
    {
      "@type": "Question",
      "name": "How do I backup my Super Productivity data before upgrading?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Navigate to Settings > Data > Export. Choose the JSON or new Markdown format to save a local backup to your machine before pulling the new Docker image."
      }
    }
  ]
}
</script>