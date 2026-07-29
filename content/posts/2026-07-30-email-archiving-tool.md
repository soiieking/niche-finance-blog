---
title: "The Ultimate Guide to Self-Hosted Email Archiving: Mailarchiva vs. Mailcow & More"
date: 2026-07-30T04:59:24+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Discover the best self-hosted email archiving tools according to Reddit. Compare Mailcow, Mailarchiva, and custom IMAP sync solutions with setup tips."
---

## The Community Spark: Why Email Archiving is Trending on r/selfhosted

Recently, a vibrant thread on r/selfhosted sparked a deep discussion about email archiving tools. The core problem? Users are terrified of vendor lock-in and silent data loss. Whether it's Google Workspace quietly deprecating features or Microsoft 365 purging inactive accounts, the community agrees: relying solely on SaaS cloud providers for historical email retention is a liability. The consensus is clear—if you run your own infrastructure, you must self-host your email archive.

## Synthesized Community Perspectives

The r/selfhosted community offered varied perspectives based on lived experiences:

*   **The "All-in-One" Advocates:** Many users praised **Mailcow-dockerized**. While primarily a mail server, its built-in SOGo Webmail and robust LDAP/SQL integration allows for implicit archiving. Users noted that keepingMailcow running on a robust VPS with daily incremental backups acts as a bare-bones archive.
*   **The Enterprise-Grade Seekers:** A vocal segment advocated for **Mailarchiva**. Designed specifically for email archiving, it handles massive volumes, legal holds, and e-discovery. However, r/selfhosted users noted it requires significant RAM and a steep learning curve.
*   **The DIY Minimalists:** Veteran sysadmins argued against running a full mail server just for archiving. Instead, they recommended syncing emails via `imapsync` to a low-cost VPS or home NAS append-only storage, avoiding SMTP port 25 blocks entirely.

## Deep-Dive Actionable Guide: Setting Up an IMAP Append-Only Archive

If you want a lightweight, read-only archive of your IMAP mailboxes without running a complex SMTP server, **`imapsync`** combined with a cron job is the most community-tested method.

Here is a practical setup to archive a Gmail account to a local Dovecot instance running on a Linux VPS.

### Step 1: Install Dovecot on the Archive Server
Log into your archive VPS and install Dovecot:
```bash
sudo apt update && sudo apt install dovecot-imapd dovecot-core -y
```

Configure Dovecot to store mail in Maildir format. Edit `/etc/dovecot/conf.d/10-mail.conf`:
```text
mail_location = maildir:~/Maildir
```
Restart Dovecot:
```bash
sudo systemctl restart dovecot
```

### Step 2: Install imapsync on the Source Server
On a separate machine (or the same VPS), install `imapsync`:
```bash
sudo apt install imapsync -y
```

### Step 3: Build the Sync Script
Create a bash script named `archive-mail.sh` to pull emails from your provider and push them to your archive VPS. Note the `--maxage` parameter, which lets you sync historical emails in batches, and `--search` to ensure we don't accidentally delete source emails.

```bash
#!/bin/bash
# Source (e.g., Gmail)
SRC_USER="you@gmail.com"
SRC_PASS="your_app_password"
SRC_HOST="imap.gmail.com"

# Destination (Your VPS)
DST_USER="archive_user@yourvps.com"
DST_PASS="secure_archive_password"
DST_HOST="your_vps_ip"

imapsync \
--host1 "$SRC_HOST" --user1 "$SRC_USER" --password1 "$SRC_PASS" \
--host2 "$DST_HOST" --user2 "$DST_USER" --password2 "$DST_PASS" \
--no-modules-version \
--sep1 "/" --sep2 "/" \
--nofoldersizes --skipmess "^Folder/" \
--syncinternaldates
```
Make it executable and run a test: `chmod +x archive-mail.sh && ./archive-mail.sh`.

### Step 4: Automate via Cron
To run the sync every Sunday at 2 AM, add it to your crontab:
```bash
0 2 * * 0 /path/to/archive-mail.sh >> /var/log/mail_archive.log 2>&1
```

## Comparative Table: Email Archiving Solutions

Based on community consensus, here is how the most popular options compare:

| Tool / Approach | Cost | Best For | Key Pros | Key Cons |
|---|---|---|---|---|
| **Mailcow** | Hardware costs | Self-hosting total mail flow | All-in-one Docker stack, active community | Requires open port 25, complex DNS (SPF/DKIM/DMARC) |
| **Mailarchiva** | Free / Enterprise | Legal compliance & e-discovery | Advanced search, legal holds, central web UI | Heavy RAM usage, Java-based, steep learning curve |
| **DIY `imapsync`** | Open Source | Minimalist, read-only archiving | Extremely lightweight, works with any IMAP, no port 25 | Custom scripts required, no built-in webmail UI |

## The Verdict: Expert Advice

The right self-hosted email archiving tool depends on your IT maturity:

1. **For the Full Self-Hoster:** If you already manage your MX records and want a complete mail server that inherently archives your data, deploy **Mailcow-dockerized**.
2. **For Compliance-Heavy Admins:** If you run a small business or need legal audit trails, **Mailarchiva** is the most robust, purpose-built tool available.
3. **For the Minimalist Archivist:** If you just want a Gmail/Outlook backup without taking on MX record responsibilities, use the **`imapsync`** cron job approach on a cheap NAS.

## Frequently Asked Questions (FAQ)

**Is self-hosting an email archive legal?**
Yes, archiving your own emails is legal. However, if you are archiving emails for employees or clients, EU GDPR or regional wiretapping laws may require consent. Always consult local compliance guidelines.

**Can I use imapsync with two-factor authentication (2FA)?**
Yes, but you must generate an "App Password" in your email provider's security settings (like Google or Microsoft) to use with `imapsync`.

**What are the storage requirements for an email archive?**
Email archiving is storage-heavy. A power user generating 50 emails a day can accumulate 5-10GB per year. A ZFS filesystem with compression enabled is highly recommended by the r/selfhosted community for email archives.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Is self-hosting an email archive legal?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, archiving your own emails is legal. However, if you are archiving emails for employees or clients, EU GDPR or regional wiretapping laws may require consent. Always consult local compliance guidelines."
      }
    },
    {
      "@type": "Question",
      "name": "Can I use imapsync with two-factor authentication (2FA)?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, but you must generate an \"App Password\" in your email provider's security settings (like Google or Microsoft) to use with imapsync."
      }
    },
    {
      "@type": "Question",
      "name": "What are the storage requirements for an email archive?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Email archiving is storage-heavy. A power user generating 50 emails a day can accumulate 5-10GB per year. A ZFS filesystem with compression enabled is highly recommended by the selfhosted community for email archives."
      }
    }
  ]
}
</script>