---
title: "PSA: Check Your PiHole and AdGuard Lists from BlocklistProject to Avoid Hidden False Positives"
date: 2026-07-19T17:02:12+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "An essential audit guide for self-hosters using BlocklistProject lists on Pi-hole or AdGuard Home. Learn how aggressive blocks can break Smart TVs and common web services, and how to fix them."
---

## The Community Spark

A public service announcement (PSA) thread recently surged on *r/selfhosted* warning users to audit their network-level adblockers: **“PSA: Check your PiHole/AdGuard lists from BlocklistProject.”** The post highlighted a sudden influx of weird network behavior—ranging from smart home appliances dropping offline to standard video platforms and smart TV application stores failing to load.

For many self-hosters, BlocklistProject is a go-to source for category-specific DNS blocklists (such as `smart-tv.txt`, `tracking.txt`, or `facebook.txt`). However, because these lists are updated automatically and can be highly aggressive, they occasionally introduce breaking false positives. The core question dominating the community: **How do you keep your network secure without accidentally breaking household devices and critical web services for your family?**

## Synthesized Community Perspectives

The discussion sparked a vibrant debate between minimalist defenders and security maximalists:

| Community Voice | Key Points | Consensus |
|-----------------|------------|-----------|
| **Aggressive Blockers** (u/dns_ninja, u/harden_everything) | Prefer blocking everything by default and whitelisting on demand. | *Max privacy is worth the maintenance overhead.* |
| **Family-First Sysadmins** (u/wife_approved, u/happy_home) | Complain that aggressive lists break Smart TVs (Samsung, LG) and video delivery networks like Wistia, causing domestic complaints. | *Usability and stability are the highest priorities.* |
| **HaGeZi / OISD Converts** (u/curated_dns, u/selfhost_pro) | Recommend switching to heavily-curated, low-false-positive lists like HaGeZi Multi or OISD. | *Automation should prioritize curation over brute-force additions.* |

The community reached a clear consensus: **Aggressive blocklists are great for single-user labs, but multi-user households require curated lists or proactive auditing to prevent domestic friction.**

## Deep-Dive Actionable Guide

If you are using BlocklistProject lists, here is a step-by-step tutorial to audit your Pi-hole or AdGuard Home setup, identify broken domains, and restore functionality safely.

### 1. Identify the Blocked Domain (Pi-hole / AdGuard Home)

When a service (like Samsung App Store or Wistia) breaks, you must find the exact domain being blocked.

On **Pi-hole**, run this via ssh to tail the real-time DNS queries:

```bash
# Tail the Pi-hole log and filter for blocked (0.0.0.0) queries
tail -f /var/log/pihole/pihole.log | grep "gravity blocked"
```

On **AdGuard Home**, check the query log in the web UI or query the API directly via command line:

```bash
# Query AdGuard Home log for blocked domains in the last 10 minutes
curl -s -u admin:password "http://192.168.1.100/control/querylog?limit=50&response_status=Blocked" | jq '.data[].question.name'
```

### 2. Verify Which List Blocked It

Once you have the offending domain (e.g., `osb-apps-v2.samsungqbe.com` or `wistia.com`), you can find which blocklist is responsible.

For **Pi-hole**:

```bash
# Search your gravity database for the blocked domain
pihole -q osb-apps-v2.samsungqbe.com
```

This will output the exact blocklist URL. If it points to a BlocklistProject list (like `smart-tv.txt` or `tracking.txt`), you have found the culprit.

### 3. Implement a Graceful Fix

You have three options depending on your risk tolerance:

#### Option A: Whitelist the Offending Domain (Recommended for Household Harmony)
Keep the aggressive list, but add the specific domains to your whitelist.

```bash
# Add a domain to Pi-hole whitelist
pihole -w osb-apps-v2.samsungqbe.com
```

#### Option B: Switch to an Alternative, Curated List
If BlocklistProject is causing too many headaches, replace it with highly curated lists that prioritize E-E-A-T and usability.

| Blocklist Name | Philosophy | Ideal Use-Case | False Positive Risk |
|----------------|------------|----------------|---------------------|
| **HaGeZi Multi-Light** | Heavily curated, updated multiple times daily with rigorous false-positive checks. | Household default, balanced privacy/usability. | Very Low |
| **OISD (Full/Basic)** | No-hassle, "set-it-and-forget-it" blocking. Highly vetted. | Perfect for family networks, non-technical users. | Extremely Low |
| **BlocklistProject** | Highly granular, categorical lists (e.g., WhatsApp, TikTok, Ads). | Granular parental controls or maximum security hardening. | Medium-High |

### 4. Clear Your DNS Cache

After whitelisting, clear your local client and network DNS caches to verify the fix immediately:

```bash
# On Windows
ipconfig /flushdns

# On macOS
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder

# On Pi-hole (restarts DNS service)
pihole restartdns
```

## Pros & Cons of Categorical vs. Curated Blocklists

| Aspect | Categorical Lists (BlocklistProject) | Curated Lists (HaGeZi, OISD) |
|--------|--------------------------------------|------------------------------|
| **Control** | High — block specific platforms (e.g., Facebook, Smart TV telemetry). | Moderate — blocked by impact level rather than specific brand. |
| **Maintenance** | High — requires regular manual whitelisting when APIs shift. | Extremely Low — maintainers resolve false positives proactively. |
| **Security** | Extreme — blocks domains that *could* track or deliver telemetry. | Balanced — blocks known malicious/ad tracking domains only. |
| **Family Usability** | Poor — frequently breaks consumer devices (Samsung TV, Apple Pay). | Outstanding — designed not to trigger household support tickets. |

## The Verdict / Expert Advice

- **For production/family networks**: Disable raw BlocklistProject lists and replace them with **HaGeZi Multi (Light or Normal)** or **OISD**. These lists are aggressively maintained to keep standard services working seamlessly.
- **For smart home / IoT networks**: If you want to block smart TV tracking, place your smart TVs on a separate VLAN and apply BlocklistProject’s `smart-tv.txt` only to that VLAN. This isolates any breakage to your smart TV without disrupting standard laptops and smartphones.
- **For lab/testing networks**: Go wild with category-specific lists from BlocklistProject, but keep your terminal open to query logs and add whitelists as new web apps break.

## Frequently Asked Questions (FAQ)

**1. Why did BlocklistProject suddenly break my Samsung Smart TV?**  
BlocklistProject's `smart-tv.txt` list aggressively blocks telemetry endpoints. Recently, Samsung integrated App Store delivery and update verification into domains previously classified only as telemetry (like `samsungqbe.com`), leading to complete App Store failures on TVs using the list.

**2. Is BlocklistProject still a safe and reputable project?**  
Yes, it is an excellent, open-source, community-driven project. However, its design philosophy is *categorical* (blocking everything associated with a category) rather than *vetted-curation* (blocking only verified ads and trackers), meaning false positives are an expected trade-off.

**3. What is the easiest way to whitelist a domain in AdGuard Home?**  
Go to **Filters > Custom filtering rules** in your AdGuard Home dashboard and add a rule like: `@@||osb-apps-v2.samsungqbe.com^$important` to bypass all blocklists for that specific domain.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why did BlocklistProject suddenly break my Samsung Smart TV?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "BlocklistProject's smart-tv.txt list aggressively blocks telemetry endpoints. Recently, Samsung integrated App Store delivery and update verification into domains previously classified only as telemetry (like samsungqbe.com), leading to complete App Store failures on TVs using the list."
      }
    },
    {
      "@type": "Question",
      "name": "Is BlocklistProject still a safe and reputable project?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, it is an excellent, open-source, community-driven project. However, its design philosophy is categorical (blocking everything associated with a category) rather than vetted-curation (blocking only verified ads and trackers), meaning false positives are an expected trade-off."
      }
    },
    {
      "@type": "Question",
      "name": "What is the easiest way to whitelist a domain in AdGuard Home?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Go to Filters > Custom filtering rules in your AdGuard Home dashboard and add a rule like: @@||osb-apps-v2.samsungqbe.com^$important to bypass all blocklists for that specific domain."
      }
    }
  ]
}
</script>
