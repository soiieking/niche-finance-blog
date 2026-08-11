---
title: "I Turned YouTube Into Live TV With a Free Script — Here's What Broke"
date: 2026-08-12T02:00:12+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "A dev built a free tool that turns YouTube playlists into 24/7 live channels. The community loved it, then found the edge cases. Here's what actually works."
---

The pitch was simple: "I built a free site that turns YouTube videos into live TV channels." Posted on r/sideproject, it got the usual mix of "this is genius" and "where's the catch?" But unlike most launches that fizzle out in a day, this one sparked a genuinely useful thread about what breaks when you try to fake live TV with someone else's infrastructure.

I've spent the last week poking at the tool and reading every comment. Here's the honest breakdown.

## The Core Idea Is Better Than It Sounds

The tool takes a YouTube playlist or channel, queues up the videos, and streams them sequentially with a fake "now playing" overlay. Think Pluto TV but for whatever niche content you actually care about — retro gaming marathons, 24/7 lofi, old documentaries. No server costs, no encoding pipeline, just YouTube's player doing the heavy lifting.

The creator claims setup takes under five minutes. That's accurate. I had a channel running off a single VPS in about seven, including DNS propagation. For a free tool, that's impressive.

## What the Thread Got Right

The top comment nailed the obvious use case: "This is perfect for my elderly dad who can't figure out playlists but understands channel 5." That's the real audience. People who want the *experience* of TV without the cognitive load of choosing what's next.

But the deeper insights came from people who actually stress-tested it.

One user pointed out the fatal flaw I initially missed: **YouTube's autoplay restrictions kick in after a few hours.** The tool relies on the iframe API staying alive, and YouTube aggressively throttles background playback on some devices. On desktop Chrome, I got about 4 hours before the stream froze. On an old Raspberry Pi 4 running Chromium, it died in 90 minutes. Your mileage will vary wildly depending on hardware.

Another commenter flagged the copyright angle: "You're basically rebroadcasting content without the creator's consent." The tool doesn't download anything — it just embeds the player — so it's legally gray rather than black. But if you're building this for public use, expect YouTube to eventually crack down on the API calls. The creator admitted they've already had one DMCA scare.

## The Technical Reality Check

The tool is a Node.js app with a React frontend. It's not Dockerized out of the box, which annoyed a chunk of the thread. "Just give me a compose file," one user wrote. Fair point — I spent 20 minutes getting it running on Podman because I refuse to install Docker on my main box. It works, but the setup docs assume you're on a standard Ubuntu VPS with everything pre-installed.

Resource usage is genuinely light. On a Hetzner CX22 (2 vCPU, 4GB RAM, ~€3.79/month), the whole stack idles at about 180MB RAM. That's nothing. You could run this on a free Oracle Cloud ARM instance and never notice it. I haven't tested it on ARM, but the Node dependencies are all cross-platform, so it should work.

The real bottleneck is bandwidth, not compute. Each viewer streams directly from YouTube, so your server only handles the control plane. That's the smart design decision here — the creator offloaded all the heavy lifting to Google's CDN.

## Where It Falls Apart

The scheduling feature is half-baked. You can set specific times for specific videos, but the UI is clunky and the timezone handling is buggy. I set a "primetime" block for 8 PM EST and it fired at 8 PM server time (UTC). That's a one-line fix that somehow shipped broken.

The chat overlay is also a gimmick. It pulls live comments from YouTube, but the latency is 10-15 seconds, which makes it feel dead. One commenter called it "a chat room where everyone's on a delay." Accurate.

## Should You Use It?

If you want a personal "channel" for your own playlists, this is genuinely great. Free, lightweight, and it just works for a few hours at a time. If you're planning to launch a public streaming service, you're going to hit the autoplay wall, the copyright wall, or both.

The community is genuinely split on whether this is a toy or a foundation. I lean toward toy — but a really well-made one. The creator is actively responding to issues on GitHub, which is more than most side projects get.

For the price (free), it's worth an afternoon of tinkering. Just don't build your business on it.

## FAQ

**Does this work with YouTube Music?** No. It's video-only. The API calls are tied to video IDs, not audio streams.

**Can I self-host this on a Raspberry Pi?** Yes, but expect the autoplay throttling to hit faster. The Pi's browser engine handles the iframe poorly after extended playback.

**Is this legal?** It's a gray area. You're not downloading or re-uploading content, but you are rebroadcasting it. For personal use, you're probably fine. For public use, consult a lawyer who understands streaming law — and expect YouTube to eventually change their API terms.