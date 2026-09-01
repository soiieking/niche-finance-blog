---
title: I Built a Vestaboard Alternative That Doesn't Cost $199/Year
date: '2026-08-27T10:00:33+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: Vestaboard is cool but pricey. Here's how I built my own split-flap display
  with zero ongoing subscription cost.
---

I love the idea of a Vestaboard. Split-flap displays are cool, retro-yet-modern, and have vibes. But here's the thing: $3,295 upfront and $199/year for a subscription to push text to it? Yeah… no. 
So, like any self-respecting indie hacker, I thought: "I can build that." Cue sleepless weekends, a lot of swearing at ESP32 Wi-Fi dropouts, and a finished product that's not perfect but works—and cost me under $500 with zero ongoing fees. Here's how I did it.
## What Vestaboard Does (and Why I Skipped It)
Vestaboard isn’t just a fancy display—it’s a platform. It connects to apps like Google Calendar, Slack, and Twitter (sorry, X 🙄) to push updates to its beautiful flippy, clacky tiles. It’s an aesthetic home control center.
The problem is, that $199/year subscription locks you into their ecosystem. Want to display your custom data without building a custom app for their API? Too bad.
I wanted something similar but simpler. A split-flap display I could control entirely, without a cloud service. Even if the world ends and the internet dies forever, my display will still flip.
## The Build Process: Blood, Sweat, and Gearing Ratios
### Step 1: Picking the Hardware
First, the guts. Most DIY split-flap options online involve 3D printing or laser cutting. I followed some Reddit suggestions and bought pre-made tiles from FlipDots (about $300 total). Way cheaper and saved me months of design. These come with mechanical mounts for 40 tiles—enough for 4 rows of 10 characters.
Servos? Ended up going with MG90S micro servos. Dirt cheap ($2.50 each on eBay). They're not as quiet as Vestaboard’s, but again: indie hacker budget.
### Step 2: The Brains
An ESP32 microcontroller is the heart of the system. This thing costs $10 and talks both Wi-Fi and Bluetooth. I used it to run the servo motors, receive data over MQTT, and handle timing logic. Bonus: it actually has more power than I needed, so it’s not tapping out like a Raspberry Pi Zero would. But fair warning—the stock Arduino libraries are temperamental. I wasted hours debugging.
Firmware? Completely custom. I used Arduino IDE and a library called AccelerateServo to get smooth motion (because basic control looks janky and robotic). All-in, I spent ~2 weeks refining motor timings.
### Step 3: Data Input
Here’s the fun bit: I set up Home Assistant to feed text strings to the display over MQTT. Home Assistant is magic. It pushes weather data, calendar events, and even snarky messages from my friends on Discord.
Since MQTT is LAN-based, no recurring subs or cloud dependencies. This works even if my ISP blows a fuse, which is a real bonus for me since they frequently do.
## Challenges: aka What Sucked
1. **Servo Noise**: Man, the MG90S sounds like a swarm of mosquitos. You can dampen vibrations a little with rubber mounts, but this is no Vestaboard. It's not "office quiet." If sound matters, budget for quieter servos.
2. **Reliability**: The ESP32 is great—in theory. But with MQTT firing off rapid updates (like a scrolling message), there’s a chance it’ll overrun the servo commands and one tile will just… get stuck. It’s rare, but when it happens, I have to reboot the whole thing like it's 1998.
3. **Assembly Nightmare**: There's a reason Vestaboard costs a fortune; calibration took weeks. Getting the tiles to line up perfectly every time is maddening. I had to model little 3D spacer clips to hold everything in place. Without those? Total chaos.
## Final Cost Breakdown
Here's where it gets good:
- Tiles: $300 (FlipDots pre-made modules, 40 tiles)
- Servos: $100 (40 MG90S @ $2.50/each)
- ESP32: $10
- Misc. wiring, mounts, and a cheap USB power supply: $50
- **Total:** $460-ish
Compared to $3,295? I’ll take it. No cloud dependency, no $199/year subscription, and I get DIY bragging rights.
## Should You Build One?
Honestly? Probably not if you value your free time. This project is a ton of work, and the result is far from plug-and-play. But if you love tinkering, this is a fun, rewarding way to roll your own solution. Plus, it makes for a hell of a fireside conversation piece.
If none of this appeals but you still want something similar, check out **LaMetric Time**. It’s an internet-connected LED display that’s less sexy than a split-flap but costs ~$200 without subscriptions.
### FAQ
#### **How long did it take to build?**
About a month of weekends. Most of that was calibrating tiles and debugging ESP32 issues.
#### **Can I adapt this for larger displays?**
Sure, but scaling means higher costs. More tiles = more servos, and you’ll need more power supply amps. Stuff’s not linear.
#### **What software do I need?**
Arduino IDE for ESP32 firmware. Home Assistant for handling MQTT. Both are open-source and free.
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "How long did it take to build?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "About a month of weekends. Most of that was calibrating tiles and debugging ESP32 issues."
      }
    },
    {
      "@type": "Question",
      "name": "Can I adapt this for larger displays?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Sure, but scaling means higher costs. More tiles = more servos, and you’ll need more power supply amps. Stuff’s not linear."
      }
    },
    {
      "@type": "Question",
      "name": "What software do I need?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Arduino IDE for ESP32 firmware. Home Assistant for handling MQTT. Both are open-source and free."
      }
    }
  ]
}
</script>
