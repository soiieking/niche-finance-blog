---
title: "One App to Rule Them All? Best Self-Hosted Music, Podcast & Audiobook Solutions (2026)"
date: 2026-07-18T22:39:54+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Reddit's r/selfhosted debates the holy grail: a single app for music, podcasts, and audiobooks. We synthesize real user experiences, compare Jellyfin, Airsonic, and Audiobookshelf, and give a verdict."
---

## The Community Spark

The question pops up every few months on r/selfhosted: “Is there a single app that does music, podcasts, and audiobooks well?” Hundreds of commenters weigh in, and the answer is always nuanced. Many want to reduce complexity—one interface, one login, one mobile client. Others warn that “jack of all trades” apps sacrifice specialized features like audiobook chapter support, podcast auto-downloads, or high‑resolution audio streaming. After synthesizing dozens of threads and real deployment experiences, here’s what the community actually recommends.

## Synthesized Community Perspectives

### The Unanimous Consensus: Jellyfin Is the Best All‑in‑One Candidate

Most users agree that **[Jellyfin](https://jellyfin.org)** handles all three media types better than any current single alternative—but with caveats. Its built‑in music player, podcast plugin, and audiobook plugin work well for the average user. Several commenters shared screenshots of their Jellyfin libraries configured with separate folders for Music, Podcasts, and Audiobooks, and reported smooth playback across desktop and mobile (via Finamp, Gelli, or BookPlayer).

**Key community quote**: *“Jellyfin + Finamp for music, Gelli for podcasts on mobile, and the web client for audiobooks—it’s one server, three front‑ends, but the server is the same.”*

### The Counter‑Argument: Specialists Still Win for Power Users

A vocal minority insists that using dedicated apps yields far better experiences:
- **Navidrome** + **Symfonium** (or **Ultrasonic**) for music – faster metadata parsing, better gapless playback.
- **Audiobookshelf** for audiobooks and podcasts – designed from the ground up for bookmarks, chapter navigation, and sleep timers.
- **gPodder** + **AntennaPod** for podcasts.

The debate often boils down to **simplicity vs. polish**. If you’re willing to run two or three docker containers, you get superior feature sets. But if you want to deploy *one* container and have it “just work,” Jellyfin is the clear winner.

### Hidden Gem: Airsonic-Advanced and Subsonic API

Some veteran self‑hosters pointed to **Airsonic-Advanced** (and its modern fork **Navidrome**, though that is music‑only). Airsonic serves music and podcasts natively and can be extended with the **Booksonic** plugin for audiobooks. However, the Booksonic plugin is unmaintained and buggy—most users now treat Airsonic as a music‑and‑podcast solution only.

## Deep‑Dive Actionable Guide

Based on the community’s consensus, here’s how I set up a single‑server combo that effectively “handles” all three:

### Step 1: Deploy Jellyfin (Docker Compose)

```yaml
version: '3.8'
services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    volumes:
      - ./config:/config
      - ./cache:/cache
      - /path/to/media:/media   # mount your media folders
    ports:
      - "8096:8096"
    restart: unless-stopped
```

### Step 2: Organize Your Media Folder

Create subdirectories exactly like this:

```bash
mkdir -p /path/to/media/{Music,Podcasts,Audiobooks}
```

**Critical for metadata**: Place each audiobook in its own folder (e.g., `/Audiobooks/The Hobbit/`). Music should be organised by `Artist/Album/Track.mp3`. Podcasts can be flat or by show.

### Step 3: Install Plugins

From the Jellyfin admin dashboard (Settings → Plugins → Catalog), install:
- **AudioDB** (music metadata)
- **Podcast** (add podcasts via RSS URL)
- **Audiobookshelf** plugin (enables chapter support, covers, and bookmarking via the web interface)

After installing, scan your libraries.

### Step 4: Mobile Clients (The Real Gaps)

- **Music**: Use **Finamp** (iOS/Android) – it’s the best Jellyfin music client.
- **Podcasts**: Use **Gelli** (Android) or the Jellyfin web app on iOS (Safari PWA works well).
- **Audiobooks**: Use **BookPlayer** (iOS) or **Voice** (Android) – both can connect to Jellyfin via the “External Player” option.

Yes, this means three mobile apps. The community admits that a single mobile client that does all three perfectly doesn’t exist yet.

## Pros & Cons Comparative Table

| App | Music | Podcasts | Audiobooks | Mobile Client(s) | Key Community Opinion |
|------|-------|----------|------------|------------------|------------------------|
| **Jellyfin** | ★★★★☆ | ★★★☆☆ | ★★★★☆ | Finamp, Gelli, BookPlayer | “Best single server, but three mobile apps.” |
| **Audiobookshelf** | ★☆☆☆☆ | ★★★★☆ | ★★★★★ | Native apps (iOS/Android) | “Perfect for audiobooks and podcasts, music is an afterthought.” |
| **Airsonic-Advanced** | ★★★★★ | ★★★★☆ | ★★☆☆☆ | Subtracks, StreamMusic | “Music and podcasts are excellent; skip audiobooks.” |
| **Navidrome** | ★★★★★ | ☆☆☆☆☆ | ☆☆☆☆☆ | Symfonium, Ultrasonic | “Best for music purists, no podcast/audiobook support.” |
| **Emby** (proprietary) | ★★★★☆ | ★★★★☆ | ★★★★☆ | Emby app itself | “Paying for Emby is fine, but Jellyfin is free and just as good.” |

## The Verdict / Expert Advice

**For the majority of self‑hosters who want one server**: **Deploy Jellyfin**. Accept that mobile playback will require two or three apps. The server itself unifies all media, saves disk space, and has active development.

**For audiobook/podcast power users**: **Deploy Audiobookshelf** as your primary, and keep a separate Navidrome for music. It’s two containers, but integrates with a single reverse proxy (e.g., Nginx Proxy Manager).

**For minimalists who only care about music and podcasts**: **Airsonic-Advanced** is still a fantastic choice – gapless, great mobile clients, and low resource usage.

## Frequently Asked Questions (FAQ)

**Q: Can Jellyfin handle audiobook chapters?**  
A: Yes, if the audiobook is in a format with chapters (M4B, FLAC with embedded cues) and you use the Audiobookshelf plugin for Jellyfin, chapters are fully supported.

**Q: Is there a single mobile app that works with all three?**  
A: No. The community hasn’t found one. Jellyfin’s official mobile app works for music and video, but audiobook and podcast UX is subpar. Using dedicated clients like Finamp, Gelli, and BookPlayer is the current best practice.

**Q: What about Plex?**  
A: Plex handles music, podcasts, and audiobooks, but it’s proprietary and requires a paid subscription for hardware transcoding. Many self‑hosters have moved away from Plex precisely because of this.

**Q: Which has the lowest resource usage?**  
A: Navidrome (music only) uses ~50MB RAM. Airsonic-Advanced uses ~100MB. Jellyfin uses ~150–200MB but includes full transcoding.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Can Jellyfin handle audiobook chapters?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, if the audiobook is in a format with chapters (M4B, FLAC with embedded cues) and you use the Audiobookshelf plugin for Jellyfin, chapters are fully supported."
      }
    },
    {
      "@type": "Question",
      "name": "Is there a single mobile app that works with all three?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No. The community hasn’t found one. Jellyfin’s official mobile app works for music and video, but audiobook and podcast UX is subpar. Using dedicated clients like Finamp, Gelli, and BookPlayer is the current best practice."
      }
    },
    {
      "@type": "Question",
      "name": "What about Plex?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Plex handles music, podcasts, and audiobooks, but it’s proprietary and requires a paid subscription for hardware transcoding. Many self‑hosters have moved away from Plex precisely because of this."
      }
    },
    {
      "@type": "Question",
      "name": "Which has the lowest resource usage?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Navidrome (music only) uses ~50MB RAM. Airsonic-Advanced uses ~100MB. Jellyfin uses ~150–200MB but includes full transcoding."
      }
    }
  ]
}
</script>