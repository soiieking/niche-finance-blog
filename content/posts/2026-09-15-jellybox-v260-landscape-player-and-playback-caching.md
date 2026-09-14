---
title: 'Jellybox v2.6.0: Landscape Player & Playback Caching — What Works, What Doesn''t'
date: '2026-09-15 02:00:05+08:00'
draft: false
tags:
- selfhosted
- media
- jellyfin
- technology
summary: Jellybox's new landscape player and playback caching have arrived. Here's
  where it shines — and where it stumbles.
---

Jellybox, one of the slickest unofficial Jellyfin clients, just dropped v2.6.0, pulling in some pretty hyped features: a landscape player mode and playback caching. I installed it immediately, because of course I did. It's like giving your media server a spa day — but real talk, not everything here is a glowing success.

Let’s break it down.

## Landscape Player: Great in Theory, Mixed in Execution

The big-headliner feature is the landscape player. Everyone in r/selfhosted is buzzing about how this makes their Jellyfin setup more like Plex or Emby’s fancy mobile apps. And it does. Kinda.

Landscape mode looks sleek—until you realize the UI doesn't scale perfectly on every device. On my Pixel 6, the buttons feel a little cramped in some spots (skip, play, etc.), and according to one commenter, "this is basically untested on tablets." I don’t have a tablet hanging around to confirm, but I wouldn’t be shocked if you ran into issues there too.

It’s nice when it works, though. Watching "Blade Runner: The Final Cut" on my phone actually felt cinematic instead of like watching a security cam recording. Just don’t be surprised if you have to fidget with phone settings or deal with some mild clipping of overlays. 

## Caching Playback: Underwhelming Performance Boost

I *wanted* playback caching to be the unsung hero of this update. Look, Jellyfin and caching have a rocky history—so many features get announced like, "this will fix stuttering forever!" and then… you get some minor improvements, but it’s not flawless. This is no different.

Does playback caching help? Sure, a little. On a low-powered VPS (2GB RAM, $5 Hetzner box), scene skipping while watching "The Sopranos" felt maybe 20-30% smoother compared to no caching. Woo. But locally on my housemate's iOS device, there was basically no change. Could be my network config, could be the app—you never totally know with Jellyfin.

That said, one redditor nailed it: "If your network sucks, this helps. But if your server is already fast, you’ll barely notice." That’s the bottom line.

## Is This Update Even Worth It?

Here’s the thing: if you already use Jellybox as your Jellyfin app of choice, 2.6.0 is a no-brainer upgrade. These are iterative quality-of-life features, not revolutionary ones. If you're running on weak hardware or often stream remotely, the caching might matter. If not, landscape mode alone probably won’t change your life.

But if you’re new to Jellybox, it’s still at the “tinker-required” stage. Don’t expect it to Just Work™ the way Plex does. You’re going to need to poke around settings, read GitHub issues, and maybe even consider alternative clients like Infuse (if, God forbid, you’re okay paying $1/month for something stable). Just being honest.

## Final Note: ARM and Edge Cases

One last wrinkle—I’m an x86 guy, so I have no idea how the landscape player or caching works on ARM devices like a Raspberry Pi. I’d wager playback caching won’t save you if you’re already transcoding on a CPU that’s sweating bullets. If you’re in this boat, tread carefully until the community weighs in. Or just hang tight and wait for 2.6.1.

---

### FAQ

#### Does playback caching drastically improve performance?

Not really. It offers incremental improvements, particularly for remote streaming on slower connections. Local playback? Barely noticeable unless your server is slower than molasses.

#### Is landscape mode good on tablets?

Unknown, but comments in r/selfhosted suggest it's unoptimized for larger screens right now. Proceed with caution.

#### Can I use Jellybox v2.6.0 with an ARM-based server?

Probably, but it hasn’t been widely tested. Playback caching likely won’t offset the strain of active transcoding on weaker ARM CPUs like a Raspberry Pi.

---
