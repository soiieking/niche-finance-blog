---
title: "Throwing Out My $40 WiFi Plastics: A 3D-Printable Irrigation Timer Built on ESP32"
date: 2026-08-05T18:00:30+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "Smart sprinklers are overpriced garbage. I built a $12 open-source ESP32 valve controller and it actually survives the rain."
---

If you’ve ever tried to automate a garden, you know the market is a joke. Commercial smart irrigation controllers are just proprietary plastic bricks charging a 400% premium for a relay switch and a crappy mobile app. 

So when I saw a post on r/sideproject where someone finally snapped and built their own 3D-printable irrigation timer, I had to look. And honestly? It’s a great example of the Maker/Indie Hacker ethos clashing with real-world constraints.

The open-source hardware project uses a bare-bones ESP32 and a basic 12V solenoid valve. Total cost per station sits around $12. Compare that to a standard Rachio 3 at $150, or even the dumbest Home Depot Orbit timer at $35. But price isn't the real story here. The trade-off is infrastructure.

### Rachio vs. The Reddit ESP32

Let’s break down the actual options if you want to water your tomatoes without standing there with a hose.

#### 1. The Commercial Route (Rachio 3 / RainBird)
Out of the box, this will take you 15 minutes to set up. It integrates flawlessly with Home Assistant via local API. It has built-in weather stations to skip a cycle if it rains. 

But it’s $150. It requires a 24V AC transformer that hardwired into your house. The enclosure is ugly as sin. One Redditor on the original thread hit the nail on the head: "I just refuse to spend the cost of a Raspberry Pi 4 just to run a valve that opens for 10 minutes a day." Fair point. You are paying for the UI and the liability insurance.

#### 2. Off-the-Shelf WiFi Timers (Sonoff, Shelly)
I love Shelly relays. I use them everywhere. A Shelly 1 on a 12V DC power supply opening a solenoid works perfectly. It costs $15 and takes literally 10 minutes to flash with custom Tasmota firmware. 

The fatal flaw? The enclosure. You can buy a $5 waterproof junction box on Amazon, but it's always a hack job. You drill a hole, hope the cable gland holds, and pray water doesn't wick into the high-voltage side. It's never built *exactly* for your specific hardware layout.

#### 3. The 3D-Printed Open-Source Valve
This is where the r/sideproject build wins. The OP printed a custom IP65-rated enclosure perfectly tailored to a standard PEST... I mean, standard irrigation valve. 

They wedged a Wemos D1 Mini ESP32 and a BTS7960 motor driver inside. Why a motor driver? Because the solenoid draws 400mA inrush current. A cheap relay module will eventually stick, or worse, arc and pit the contacts. A MOSFET-based driver handles the load elegantly and solid-state. 

Code is compiled via PlatformIO. It exposes a simple MQTT topic to Home Assistant. Open valve. Close valve. Done.

## The Gotchas Nobody Mentions

I love this project, but it has one massive, glaring flaw that everyone glossed over in the thread: **Water and electricity are a terrifying combination.**

On a breadboard in your basement, a 12V DC power supply is harmless. Outside, buried in dirt where it rains? 

I haven't tested their specific gasket design, but in my experience, printed PLA isn't going to survive a full summer in the sun sitting next to heating asphalt. It will warp. If you print this, spend the extra $15 on a roll of PETG or ASA. PLA will literally droop and snap under tension by August.

There's also the power supply question. The OP just "hooked up a converted ATX power supply." Please, for the love of god, don't do this. 

ATX power supplies are not meant to be outdoors. They are fire hazards if they get wet or short out. If you're actually going to build this, grab a proper IP67-rated 12V LED driver off Amazon for $20. Non-negotiable.

## Final Thoughts

Is this overkill for most people? Absolutely. If you just want to water your lawn while you're on vacation, go buy a $35 Orbit timer from Home Depot and set the dial. 

But if you want granular API control over your zones, want to integrate soil moisture sensors directly into your weather logic, and don't want to pay subscription fees for an iPhone app? This is the exact way to do it. Just use PETG.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [{
    "@type": "Question",
    "name": "Can I use a 3D-printed PLA enclosure for my DIY irrigation valve?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "No, PLA will warp and degrade in direct sunlight and summer heat. If you are 3D printing an outdoor enclosure for an ESP32 irrigation controller, you should use PETG or ASA to ensure it doesn't melt or snap under tension."
    }
  }, {
    "@type": "Question",
    "name": "What power supply should I use for an outdoor DIY irrigation controller?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Do not use a converted ATX power supply outdoors. You should use an IP67-rated 12V DC LED driver or a dedicated weatherproof transformer to avoid fire hazards and water damage."
    }
  }, {
    "@type": "Question",
    "name": "Why use a MOSFET driver instead of a relay for a DIY sprinkler timer?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Solenoid valves draw a high inrush current (around 400mA). Mechanical relays can pit and stick over time due to electrical arcing. A solid-state motor driver or MOSFET handles the load more reliably and lasts longer."
    }
  }]
}
</script>