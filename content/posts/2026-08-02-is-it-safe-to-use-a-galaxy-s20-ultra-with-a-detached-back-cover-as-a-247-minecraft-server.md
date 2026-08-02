---
title: "Running a Minecraft Server on a Gutted Galaxy S20 Ultra: Genius or Glorified E-Waste?"
date: 2026-08-02T16:21:04+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "homelab", "android"]
summary: "Someone on r/selfhosted wants to run a 24/7 Minecraft server on a stripped-down Galaxy S20 Ultra. Here is why that is a beautiful, terrible idea."
---

I spend an unhealthy amount of time scrolling through r/selfhosted. Usually, it is the same rotation of Raspberry Pi clusters and Plex servers. But every once in a while, someone asks a question so beautifully unhinged that I have to stop and stare. 

This week, a user asked if it is safe to use a Galaxy S20 Ultra with a detached back cover as a 24/7 Minecraft server. 

The short answer is yes. The long answer is that you are actively turning a flagship phone into an undercooked space heater.

## Phone hardware is actually perfect for this (on paper)

Let's look at the specs of the S20 Ultra. You are working with a Snapdragon 865 or Exynos 990, 12-16GB of LPDDR5 RAM, and UFS 3.0 storage. For a Minecraft server, that is beefy. A vanilla Paper server with a few plugins will eat maybe 2GB of RAM and barely touch the CPU.

A standard Raspberry Pi 4 with 8GB of RAM costs $80. You can pick up a cracked S20 Ultra on eBay for $120. In terms of raw single-core performance, the phone annihilates the Pi. Minecraft servers heavily tax a single CPU core, so this actually matters. You can absolutely run the server via Termux or a chroot Ubuntu environment, and the performance will be snappy. 

On paper, this is e-waste upcycling at its absolute peak.

## The thermal reality of a detached back cover

Here is where the r/selfhosted crowd started tearing the idea apart. 

Phones are not built to run sustained 100% CPU loads. They are built for 10-second bursts to load a webpage, then they aggressively throttle down. The original poster thinks detaching the back cover solves the thermal issue by exposing the motherboard.

It does not. 

Several commenters pointed out that the Exynos 990 is notorious for running hot. The glass back of an S20 Ultra acts as a massive passive heat spreader. The actual processor is sandwiched under layers of shielding, the motherboard, and thermal paste. Removing the plastic back gives you access to the bare motherboard, but the SoC is still trapped underneath.

If you throw a $15 Noctua 40mm fan directly over the exposed motherboard, you will survive. If you leave it sitting on your desk under passive load, Exynos 990 will thermal throttle within 20 minutes. Ticking frequencies down, TPS dropping, and eventually kernel panics. I haven't tested this specific Exynos setup myself, so your mileage may vary, but I have burned out enough old Pixel boards to know passive cooling on mobile silicon is a death sentence for 24/7 loads.

## Just buy a VPS, seriously

I love a creative junk build. But a Minecraft server demands 99% uptime so your friends can log in at 3 AM. 

If you are using a detached phone, you are now babysitting hardware. Android OS prioritizes battery life over background processes. You will be fighting Doze mode, battery management, and thermal throttling constantly. 

For $4.50 a month, you can get a Hetzner Cloud CX22. Two AMD vCPUs, 4GB of RAM, and 40GB of NVMe storage. You bypass the Android battery system entirely. You bypass the Exynos thermal throttle. You get a guaranteed SLA and easy Docker integration.

I run my Paper 1.20.4 server in a Docker container on a Hetzner box. It takes 5 minutes to deploy. Moving that same workflow to a gutted Android phone means dealing with intermittent Wi-Fi drops, cellular IP changes, and a battery that will eventually swell up like a balloon if you leave it plugged in constantly without a bypass.

## Should you do it?

If you want a fun weekend project, go for it. Stick a fan on it, install LineageOS, bypass the battery, and host a LAN world for your friends. 

If you actually want a reliable 24/7 server for a public community, stop torturing mobile silicon. Save your pennies, grab a Hetzner VPS, and let the S20 Ultra rest in peace. 

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Can you run a Minecraft server on an old Android phone?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, you can run a Minecraft server on an old Android phone using an app like Termux or a Linux chroot environment. Phones like the Galaxy S20 Ultra have fast single-core CPUs and plenty of RAM, which handles Minecraft server workloads well. However, sustained 24/7 loads will cause severe thermal throttling unless you actively cool the motherboard."
      }
    },
    {
      "@type": "Question",
      "name": "Does removing the back cover of a phone help with cooling?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Removing the back cover exposes the motherboard but does not expose the actual SoC (processor), which is usually sandwiched under shielding and thermal paste. It helps slightly, but you still need active cooling like a small fan blowing directly on the exposed motherboard to prevent thermal throttling during 24/7 server loads."
      }
    },
    {
      "@type": "Question",
      "name": "Is it cheaper to self-host on an old phone or rent a VPS?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "While the phone is a one-time cost if you already own it, renting a VPS like a Hetzner CX22 costs about $4.50 per month. For reliability, avoiding battery degradation, and bypassing Android OS battery management restrictions, a cheap cloud VPS is a significantly better investment for a 24/7 server."
      }
    }
  ]
}
</script>