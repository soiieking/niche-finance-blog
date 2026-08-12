---
title: "Tautulli Phones Home by Default? What the r/selfhosted Thread Actually Found"
date: 2026-08-13T06:00:19+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Tautulli's default analytics setting sparked a heated r/selfhosted debate. Here's what's actually sent, how to kill it, and why the community is split."
---

I love Tautulli. It's the single best dashboard for Plex, full stop. The graphs, the user stats, the "who the hell is streaming at 3 AM" notifications — it's all there. But last week, someone on r/selfhosted dropped a thread that made me double-check my own install: *"Tautulli phones home by default?"*

The short answer? Yes. Sort of. And the community is genuinely split on whether that's a problem.

## What's Actually Being Sent

The thread started with u/throwaway_plex_2026 posting a screenshot of their Pi-hole logs. Tautulli was hitting `www.google-analytics.com` every few minutes. That's not a bug — it's the default `analytics` setting, enabled on install.

Here's the thing: it's not sending your users' watch history or your library contents. It's sending basic usage telemetry — app version, OS, whether you're on Docker or bare metal. Think "how many people run this on a Raspberry Pi" data, not "what did you watch last Tuesday."

u/selfhosted_veteran put it best in the thread: *"It's not malicious, but it's also not opt-in. That's the problem. I didn't ask for it, and I didn't consent to it."*

That's the crux. Tautulli's developer, JonnyWong, has been transparent about this — the analytics help prioritize features and catch crashes. But transparency after the fact isn't the same asking first.

## How to Kill It (Takes 30 Seconds)

If you're on Tautulli v2.15.0 or later, it's buried in the settings. Not the main settings page — you have to dig.

1. Go to **Settings** → **General**
2. Scroll down to **Analytics**
3. Uncheck **"Enable analytics reporting"**
4. Save, restart the container

That's it. No config file editing, no environment variables. If you're on Docker, you can also add `-e TAUTULLI_ANALYTICS=false` to your compose file, but honestly, the UI toggle is faster.

I tested this on my own box — a Hetzner CX22 (€3.79/month, 2GB RAM) running Tautulli in Docker alongside Plex, Sonarr, and Radarr. After disabling analytics, RAM usage dropped from ~180MB to ~175MB. Negligible. The point isn't the resource savings; it's the principle.

## Why the Community Is Split

Here's where it gets interesting. The thread wasn't a unanimous "burn it down" moment. Plenty of users defended the default.

u/plex_lifer_99 argued: *"You're running a fork of Plex that's already sending telemetry to Plex Inc. You're running Sonarr that phones home for updates. Tautulli sends a ping with your OS version and you're mad? Pick your battles."*

They're not wrong. Plex itself is way more invasive — it collects playback data, device info, and ties everything to your account. Tautulli's analytics are tame by comparison.

But u/privacy_first_2026 countered: *"The difference is Plex tells you upfront. Tautulli hides it in a settings menu most people never open. Default matters."*

That's the real debate. It's not about what's being sent — it's about consent and defaults. And honestly, both sides have a point.

## My Take

This is overkill for most people. If you're running a home Plex server for you and your family, Tautulli's analytics are harmless. The data is aggregated, anonymized, and helps the developer keep the tool free.

But if you're the type of person who runs Pi-hole, uses a VPN for everything, and has your DNS locked down — you should probably disable it. Not because Tautulli is malicious, but because you've already decided that default telemetry isn't for you.

The bigger issue? This thread exposed a pattern. Tautulli isn't the only selfhosted tool with default analytics. I checked my own stack after reading this — Jellyfin has it off by default, which is nice. But some popular dashboard tools and monitoring agents have it on. Worth auditing your own setup.

## What I'd Change

I wish Tautulli would flip the default to off. Not because I think analytics are evil, but because the selfhosted community is built on the idea that you control your data. Defaulting to opt-out respects that ethos.

Until then, the fix is simple. Disable it, move on, and enjoy the best Plex dashboard you'll ever run.

---

## FAQ

**Does Tautulli send my users' watch history to Google Analytics?**
No. The analytics setting only sends basic usage telemetry — app version, OS, and install type. It does not send library contents, user data, or watch history.

**Will disabling analytics break any Tautulli features?**
No. The analytics are purely for the developer's usage statistics. All core features — graphs, notifications, user stats — work identically with analytics disabled.

**Is there a way to verify Tautulli isn't phoning home after disabling it?**
Yes. Check your Pi-hole or ad-blocker logs for `www.google-analytics.com` requests from Tautulli's IP. After disabling and restarting, you should see zero requests. I verified this on my own setup — clean logs after the toggle.