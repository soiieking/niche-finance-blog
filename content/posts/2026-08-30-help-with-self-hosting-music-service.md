---
title: 'Self-Hosting a Music Service: What Actually Works (and What Doesn''t)'
date: '2026-08-30T20:00:50+08:00'
draft: false
tags:
- selfhosted
- media server
- music
- technology
summary: 'Tried, broken, and fixed: the real deal on self-hosting a music server without
  losing your mind.'
---

## So, You Want to Self-Host Your Tunes?
Cool. I get it. Spotify's UI is annoying, you panic every time they announce another price hike, and there's this nagging feeling that having **all your music local** is just... right. More control, no ads, maybe even better sound if you're an audiophile. I've been there.
But self-hosting music? That’s its own bag of headaches. The good news? There are tools for this. The bad news? Most of them are a compromise in some way. Here’s where I’ve landed after trying more solutions than I want to admit.
## First Things First: Why Self-Host?
If you're already in /r/selfhosted, you know the deal. The usual reasons are:  
- **Privacy**: No giant company logging your music taste.
- **Offline access**: No internet? No problem.
- **Customization**: Maybe you want fewer wizards and more knobs to tweak. Fair.
The heat comes from the storage and management side. If you’re not careful, your music server will turn into that neglected corner of your hard drive where MP3s go to die. And that’s before we even talk about transcoding.
## The Big Three: Navidrome, Jellyfin, and Plex
I’ve narrowed this down to three categories of tools that make sense in 2026: **Navidrome**, **Jellyfin** (yep, it’s not just for movies), and the over-engineered media overlord, **Plex**. If you mess with Subsonic derivatives, that’s another rabbit hole, but tread with caution — they can be janky as hell.
### Navidrome: Light, Fast, Opinionated
Navidrome is the darling of this space. It’s a Subsonic-compatible server written in Go, and it’s fast. Like, absurdly fast. It chews through large libraries (mine’s ~400GB across 30k tracks), and the web interface feels snappy even on my dusty old ThinkPad running Debian.
Setup is dead simple with Docker:  
```bash
docker run -d \
  -p 4533:4533 \
  -v /path/to/music:/music \
  -v /path/to/data:/data \
  deluan/navidrome
```
What’s the catch? There’s no out-of-the-box mobile app. You’ll have to snag something like DSub or Ultrasonic for Android, or fork over for iSub on iOS. The web client is fine on desktop, but the mobile experience feels lacking.
**Who’s it for?**  
- Minimalists. No video, no fluff.  
- People with ~1GB of RAM to spare.  
- Folks who *already* have a decent library tagged correctly. (Navidrome will not clean your metadata. It assumes you're a grown-up.)
### Jellyfin: Your DIY Swiss Army Knife  
Jellyfin is mostly known for movies/TV, but you can absolutely use it for music — I’ve done it. The nice part is that it handles mixed media libraries well, so if you already run Jellyfin, just point it at your music folder.
The Web UI supports playlists, offline downloads, and multi-user profiles. Jellyfin’s biggest advantage over Navidrome is its ecosystem, particularly native streaming apps like Gelli (Android/iOS), which works well for music.  
One downside: Jellyfin’s music scanning is c-r-a-w-l-i-n-g slow. My 400GB library took 7 hours to catalog on an x86 box. RAM-wise, Jellyfin with music switched on hovers around 2–2.5GB, so it’s heavier than Navidrome.
**Who’s it for?**  
- People who want one media app to rule them all.  
- Families (multi-user support rocks).  
- People with beefier servers. Don’t try this on a Raspberry Pi unless you hate yourself.
### Plex: Overkill for Music?
Look, Plex is… Plex. It’s slick, and the mobile experience is polished AF. But using Plex **just for music** is like driving a McLaren to get groceries. It’ll work, sure, but it’s resource-hungry and assumes you want 19 other features (Live TV, movies, photos...). Cue inevitable complaints in /r/selfhosted about Plex "growing into bloatware."
Still, Plexamp (their dedicated mobile app for Plex music) is next-level cool. It supports gapless playback, smart playlists, and nifty things like "sonic similarity" (basically AI-generated mixes). If you have $5/month to burn, Plex Pass unlocks this, but you’re tethering yourself to their ecosystem.
**Who’s it for?**  
- People with disposable income.  
- Audiophiles who use FLAC and care about transcoding/buffering speeds.  
- People already using Plex for video — the integration is seamless.
## Should You Host On a VPS?
Short answer: Probably not for music. This question came up in the thread I read, and most users agree it’s overkill unless you’re really constrained on local storage. Trying to stream 50GB+ of FLAC from Hetzner will get old fast — your bottleneck will likely be your bandwidth.
Stick to local hosting. Even a Raspberry Pi 4 can handle Navidrome for smaller libraries. Mine runs fine on a refurb Lenovo M93p Tiny with 8GB RAM ($120 on eBay).
## And the Winner Is...
For me? **Navidrome**. The simplicity wins. But if you’re deep in the Jellyfin ecosystem, sticking to one app makes sense. Plex only makes sense at scale. 
Your mileage **will** vary depending on how big your library is and how much tinkering you actually enjoy. Here's my advice: don’t overthink it. Get one running in under an hour and try it. The worst-case scenario? You dismount a Docker container and move on.
## FAQ (How to Not Lose Your Sanity)
### **What’s the smallest server this will run on?**
Navidrome wins here — I’ve run it on a Pi Zero 2 W, though it struggled with simultaneous high-bitrate streams.
### **Can I stream FLAC?**
Yes, but it depends on your setup. Plex handles FLAC-to-MP3 conversion well. Navidrome supports it outright, but bandwidth *will* throttle you on any remote server.
### **Will my metadata survive this?**
Not unless you clean it first. Always tag your music before importing — use Picard or be ready for chaos.
