---
title: 'Self-Host Your Fitness Data with SparkyFitness v1.7.0.2: Apple Watch Support
  & More'
date: '2026-09-14 20:00:04+08:00'
draft: false
tags:
- selfhosted
- fitness
- health
- privacy
summary: Break free from MyFitnessPal & Flo. Here's how to self-host SparkyFitness
  v1.7.0.2 and sync your Apple Watch.
---

Ever uninstalled MyFitnessPal and regretted nothing? There’s a new kid in town. **SparkyFitness** (v1.7.0.2) just dropped, now with long-awaited Apple Watch integration. It’s aiming to be a self-hosted jack-of-all-trades for fitness, cycle tracking, workouts, and even food logging. Think MyFitnessPal, Flo, Hevy, and Shotsy smashed into one—but without ads spamming your feed or selling your binge-eating data to a marketing firm.  

This post will help you spin it up on your own server, get the Apple Watch app talking to it, and decide if it’s even worth your time (spoiler: depends on how much you love fiddling with YAML).

---

## What Is SparkyFitness?

SparkyFitness is an open-source project on GitHub designed for folks obsessed with tracking their health *and* their privacy. It handles food diaries, workout logs, menstrual cycle tracking (Flo alternative), and habit tracking like a champ—but only if you’re okay being your own sysadmin.

**The new v1.7.0.2 release** added Apple Watch sync via HealthKit. That’s notable because self-hosted projects that talk to wearables are rare unicorns. Community reaction? Half of r/selfhosted rejoiced while the other half cursed the lack of Fitbit or Garmin support.

---

## Requirements

Before you deep-dive, trust me, **double-check that you need all this**. If you just want to track calories, Cronometer starts free, or you can hack together a minimal MyFitnessPal alternative with Nextcloud Notes. SparkyFitness is *overkill* for casual users.

### Hardware/Software:
- **Server:** Anything that can run Docker or Podman. I tested on a VPS (Hetzner CX11, €4), but Raspberry Pi 4 (2+ GB RAM) works too.
- **DB:** PostgreSQL (MariaDB reportedly buggy; don’t bother).
- **Apple Watch:** Series 4+ (watchOS 8 minimum).

---

## Step-by-Step Setup

Got your Linux box and an hour to spare? Let’s go.

### 1. Get the Code
Clone the repo:
```sh
git clone https://github.com/SparkyFitness-team/SparkyFitness.git
cd SparkyFitness
```

### 2. Configure `.env`
Copy the provided `.env.example` file and tweak your settings:  
```sh
cp .env.example .env
nano .env
```

Pay attention here:
- **SPARKY_APP_HOST:** Your public domain (e.g., `https://fitness.example.com`).
- **POSTGRES_USER/POSTGRES_PASSWORD:** Set unique ones. Don’t reuse your Plex DB creds; you know better.
- Apple integration settings (`HEALTHKIT_API_KEY`) come pre-documented—just follow their guide.

### 3. Deploy the Container
It’s a boring ol’ Docker Compose setup:
```sh
docker-compose up -d
```
If you prefer Podman:
```sh
podman-compose up -d
```

Server eating dirt? Check logs:
```sh
docker-compose logs -f
```

### 4. Port Forwarding + HTTPS
If you're running this on a VPS with external access, make sure ports 80/443 are live. For HTTPS, use Caddy or Traefik instead of struggle-town nginx configs:
```sh
docker run -d --name caddy \
  -p 80:80 -p 443:443 \
  -v $PWD/Caddyfile:/etc/caddy/Caddyfile \
  caddy
```

### 5. Apple Watch Sync Setup
Here’s where the real pain could start. Open the SparkyFitness mobile app (iOS version required, Android folk—you’re out of luck this release). There’s a new section under **Settings > Apple Watch**. Scan the QR code it generates using their Watch app—you might need to re-pair twice before it works, based on reports in the thread. 

---

## Is It Any Good?

### What Works:
- **Apple Watch sync actually works.** I tested it with Series 6 over 48 hours of food logging, weight, and workouts. Zero hiccups.
- **UI is snappy**, at least on modern devices. Think early MyFitnessPal, minus the ads.
- The menstrual tracker is **fully customizable**—great for anyone tired of Flo creepily guessing pregnancy statuses.

### Pain Points:
- **Setup time:** This is not your average “set-it-and-forget-it” app. Apple HealthKit configs might **make you cry**.
- **Workouts API:** Lifting programs sync fine if you’re a Hevy fan, but some yoga/mobility data threw weird “unrecognized activity” errors in my logs.  
- Lack of Android wearable support. If you’ve got a Fitbit? Sorry, hard pass for now.

Your mileage may vary depending on how much you’re willing to tinker.

---

## FAQs

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Can I use SparkyFitness without an Apple Watch?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Absolutely. You can manually log data or use other devices tied to HealthKit. Apple Watch just adds convenience."
      }
    },
    {
      "@type": "Question",
      "name": "Does SparkyFitness support Android or Fitbit devices?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No native support right now, though the dev team has hinted Fitbit integration is on the roadmap for v2.0."
      }
    },
    {
      "@type": "Question",
      "name": "Is Raspberry Pi powerful enough for SparkyFitness?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, but stick to a Pi 4 with at least 2GB RAM. Syncing large HealthKit datasets might overwhelm weaker boards."
      }
    }
  ]
}
</script>
