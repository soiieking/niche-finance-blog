---
title: "Scaling Nextcloud for 15 Employees: Battle-Tested Deployment Best Practices"
date: 2026-07-30T07:02:25+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Deploying Nextcloud for a 15-person company requires careful planning. Discover community-vetted best practices for architecture, caching, and backups to ensure uptime."
---

## The Community Spark

A recent trending post in r/selfhosted asked a critical question: *"What are the best practices for deploying Nextcloud in a small company with 15 employees?"* While Nextcloud is a powerhouse for data sovereignty, scaling it from a single-user hobbyist setup to a business-critical application for 15 concurrent users introduces unique bottlenecks. The community responded with a mix of hard-learned lessons and enterprise-grade architectural tweaks.

## Synthesized Community Perspectives

The r/selfhosted consensus was clear: out-of-the-box Nextcloud installations crumble under concurrent business use. The primary debates centered on architecture.

**The Bare Metal vs. LXC/VM Debate:**
A faction of users argued for bare metal or direct Docker installs for maximum I/O performance. However, the overwhelming consensus favored Proxmox LXC (Linux Containers) or dedicated VMs. Isolating Nextcloud from the host OS simplifies backups and prevents dependency hell.

**The Calendar Sync Bottleneck:**
Multiple sysadmins highlighted a non-obvious pain point: calendar and contact syncing. 15 users aggressively syncing devices can spike CPU load. The community unanimously agreed that decoupling the database and utilizing Redis isn't optional—it's mandatory for business deployments.

## Deep-Dive Actionable Guide

Based on community battle-scars, here is the architectural blueprint for a 15-user enterprise deployment.

### 1. Architecture & Prerequisites
Do not use SQLite. Do not use MySQL. Use **PostgreSQL**. Pair this with **Redis** for in-memory caching to prevent database lockups during concurrent file and calendar syncs.

### 2. Docker Compose Configuration
Deploying via Docker Compose ensures reproducibility. Below is a hardened, community-vetted starting point for your `docker-compose.yml`:

```yaml
version: '3.8'

services:
  db:
    image: postgres:15-alpine
    restart: always
    volumes:
      - ./db_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=nextcloud
      - POSTGRES_USER=nextcloud
      - POSTGRES_PASSWORD=YourStrongPasswordHere

  redis:
    image: redis:alpine
    restart: always

  app:
    image: nextcloud:apache
    restart: always
    ports:
      - 8080:80
    volumes:
      - ./nextcloud_data:/var/www/html
    environment:
      - POSTGRES_HOST=db
      - REDIS_HOST=redis
      - POSTGRES_DB=nextcloud
      - POSTGRES_USER=nextcloud
      - POSTGRES_PASSWORD=YourStrongPasswordHere
    depends_on:
      - db
      - redis
```

### 3. Nginx Reverse Proxy & Security
Always place Nextcloud behind a reverse proxy like Nginx Proxy Manager or Traefik to handle SSL termination. Add the following to your `config.php` to trust the proxy and enforce security:

```php
'trusted_proxies' => ['proxy-network-ip'],
'overwriteprotocol' => 'https',
'enforce_trusted_domain' => true,
```

### 4. The 3-2-1 Backup Strategy
A 15-person company generates critical data. A simple volume snapshot is not enough. Implement a 3-2-1 strategy using tools like **BorgBackup** or **Restic** to push encrypted, deduplicated backups offsite to an S3-compatible provider (e.g., Backblaze B2). 

## Pros & Cons: Deployment Strategies

| Approach | Pros | Cons | Best For |
| :--- | :--- | :--- | :--- |
| **Proxmox LXC** | Minimal overhead, easy snapshot backups, high I/O speed. | Slight learning curve for permissions. | Proxmox admins seeking max performance. |
| **Docker Compose** | Highly portable, simple version control, easy to rebuild. | Slight storage I/O penalty via overlayfs. | Hybrid cloud or multi-host setups. |
| **AIO (All-in-One)** | Officially supported, built-in high-availability features. | Heavy, strictly manages its own containers. | Admins wanting an "easy button". |

## The Verdict / Expert Advice

For a 15-employee company, **Docker Compose with PostgreSQL and Redis** is the sweet spot. It provides enough isolation and performance without the overhead of a full Kubernetes cluster or the rigidity of Nextcloud AIO. 

Most importantly, appoint a designated IT responsible party. Nextcloud updates can break apps; testing updates on a cloned staging environment before pushing to production is what separates a business tool from a weekend hobby.

## Frequently Asked Questions (FAQ)

**What are the minimum hardware requirements for Nextcloud for 15 users?**
At minimum, a dual-core CPU, 4GB to 8GB of RAM, and fast SSD storage. The database and PHP processes require quick I/O to handle concurrent calendar and file syncs efficiently.

**Can I use Nextcloud Office for a 15-person company?**
Yes. You can integrate Collabora or OnlyOffice via Docker. While it adds RAM overhead (at least 2GB extra for the document server), it provides a fully self-hosted alternative to Google Workspace.

**How do I handle Nextcloud updates safely?**
Always back up the database and the `/var/www/html` volume before updating. Ideally, run a staging instance or use Nextcloud AIO's built-in backup mechanism to roll back if an update breaks mission-critical apps.

**Does Nextcloud handle large file uploads out of the box?**
No. You must adjust PHP configuration settings (`upload_max_filesize`, `post_max_size`) and your Nginx/Apache reverse proxy settings (`client_max_body_size`) to support large files.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What are the minimum hardware requirements for Nextcloud for 15 users?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "At minimum, a dual-core CPU, 4GB to 8GB of RAM, and fast SSD storage. The database and PHP processes require quick I/O to handle concurrent calendar and file syncs efficiently."
      }
    },
    {
      "@type": "Question",
      "name": "Can I use Nextcloud Office for a 15-person company?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. You can integrate Collabora or OnlyOffice via Docker. While it adds RAM overhead (at least 2GB extra for the document server), it provides a fully self-hosted alternative to Google Workspace."
      }
    },
    {
      "@type": "Question",
      "name": "How do I handle Nextcloud updates safely?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Always back up the database and the /var/www/html volume before updating. Ideally, run a staging instance or use Nextcloud AIO's built-in backup mechanism to roll back if an update breaks mission-critical apps."
      }
    },
    {
      "@type": "Question",
      "name": "Does Nextcloud handle large file uploads out of the box?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No. You must adjust PHP configuration settings (upload_max_filesize, post_max_size) and your Nginx/Apache reverse proxy settings (client_max_body_size) to support large files."
      }
    }
  ]
}
</script>