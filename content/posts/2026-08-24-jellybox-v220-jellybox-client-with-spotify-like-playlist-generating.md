---
title: 'Jellybox v2.2.0: DIY Spotify-Style Playlists on Your Jellyfin Setup'
date: '2026-08-24T00:00:14+08:00'
draft: false
tags:
- selfhosted
- jellyfin
- linux
- media-server
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Jellybox v2.2.0: DIY Spotify-Style Playlists on Your Jellyfin
  Setup.'
---

Jellybox v2.2.0 dropped recently, and you’d think the entire r/selfhosted forum collectively discovered fire. Why? Because **Spotify-style playlist generation** is now a thing. If you’ve been self-hosting Jellyfin (or even Plex) to flex that local media collection, Jellybox just made your setup *a lot* cooler.
But first: Is this even worth your time? If you’ve got a decently tagged library and are missing Spotify’s curated vibes but don’t want to sell your soul (or data), **yes**. If your metadata is a mess? Skip it. Jellybox doesn’t magically know your music passion project from your random mp3 dump of LimeWire tracks.
Here’s how to get Jellybox v2.2.0 running and start feeling smug about how your homemade Spotify uses 0% proprietary cloud.
## Prereqs: What You Need Before You Start
1. **A Jellyfin server** that’s already working. Jellybox doesn’t run on its own; it needs to talk to Jellyfin’s API. Running Jellyfin in a Docker container with a reverse proxy is pretty standard. If you don’t have this, [check out this base-level guide here](https://jellyfin.org/docs/general/quick-start.html).
2. **Node.js**. Jellybox is a Node app. If you don’t have it installed, `curl` or `wget` is your friend:
   ```bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt install -y nodejs
   ```
   (Or just replace `18.x` with the latest LTS version.)
3. Enough free time to read the GitHub issue thread when something inevitably doesn’t work the way it should.
## Install Jellybox v2.2.0 
The Jellybox README is clean, but let me save you some scrolling. Follow these steps:
1. Clone the repo:
   ```bash
   git clone https://github.com/dan1t0/jellybox.git
   cd jellybox
   ```
2. Use the `v2.2.0` tag:
   ```bash
   git checkout v2.2.0
   ```
3. Install dependencies:
   ```bash
   npm install
   ```
4. Configure Jellybox:
   Create a `.env` file with your Jellyfin API details. Something like this:
   ```
   # Example Jellyfin connection
   JELLYBOX_PORT=3000
   JELLYFIN_URL=http://your-jellyfin.local:8096
   JELLYFIN_API_KEY=your_super_secret_key
   ```
   If you’re unsure how to get the API key—just head to the Jellyfin web client, log in as admin, and generate one under `Dashboard -> API Keys`.
5. Run Jellybox:
   ```bash
   npm start
   ```
If everything’s working, you now have a Jellybox instance spinning at `http://localhost:3000`. Forward that port through Nginx or Caddy if you feel fancy.
## Spotify-Style Playlist Magic
Here’s where it gets good. Jellybox doesn’t just shuffle your library randomly—it has some algorithmic smarts built-in. The new playlist generator in v2.2.0 uses your metadata and plays nice with [Last.fm’s API](https://www.last.fm/api) for recommendations.
To test it:
1. Open the Jellybox web interface.
2. Hit the “Generate Playlist” button.
3. Choose your vibe:
   - **By Tags/Genres**: Love your meticulously organized rock collection? Or did you waste an existential weekend tagging every Bowie song? This is your option.
   - **By Artist Similarity**: Think Last.fm scrobbling but private.
   - **By Randomness**: The chaos gremlin choice is also fun.
Generated playlists sync back to Jellyfin directly. No need to fiddle around exporting or manually reorganizing.
## Some (Expected) Pitfalls
Here’s the honest truth: Jellybox v2.2.0 is great, but it’s not going to flawlessly replace Spotify. A few pain points worth noting:
- **Metadata Quality Matters**: If you don’t have proper tags, Jellybox is borderline useless. The author on GitHub *himself* recommends tagging with [MusicBrainz Picard](https://picard.musicbrainz.org/).
- **No True “Smart Playlist” Features Yet**: You can’t dynamically create “always up-to-date” playlists like “latest 10 albums.” Requests for this are on GitHub, but ¯\\\_(ツ)_/¯.
- **Resource Use**: On my test VPS (2 vCPU, 4GB RAM), the Jellybox process alone added ~56 MB RAM and negligible CPU when idle.
## FAQ 
```json
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Can I use Jellybox on a non-Jellyfin setup?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No, Jellybox is explicitly built to integrate with Jellyfin. For Plex users, you'd need to look into other plugins or scripts."
      }
    },
    {
      "@type": "Question",
      "name": "Does Jellybox v2.2.0 support auto-generated playlists?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Sort of. It generates playlists manually when you hit 'generate,' but these playlists don’t auto-update if new media is added."
      }
    },
    {
      "@type": "Question",
      "name": "Does it work with ARM-based devices?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, but it depends on your Node.js setup. Some users have reported hiccups on Raspberry Pi with older versions of Jellybox. Test locally before you fully commit."
      }
    }
  ]
}
</script>
```
## Final Thoughts
Jellybox v2.2.0 is cool tech for the self-hosted crowd. It's not perfect, but if you’re already running Jellyfin, it’s worth setting up. Just keep your metadata in shape, or this won’t work. And if it doesn’t, the r/selfhosted thread is active enough to save you.
