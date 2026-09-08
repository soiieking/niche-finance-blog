---
title: 'Building a Tiny Live Transit Board: Fun Hack or Desk Overkill?'
date: '2026-09-08 18:00:04+08:00'
draft: false
tags:
- indie-hacker
- hardware
- side-projects
summary: I made a live public transport departure board for my desk. Was it worth
  it? Here's what worked, what broke, and what I'll do next.
---

## Why Make a Live Transit Board?

Let’s be honest, this is not a practical project for 99.5% of people. You can just Google your train schedule, right? But there’s something about a tiny device, sitting on your desk, updating live — a mix of nerdy charm and unapologetic over-engineering. 

Plus, it’s a great conversation starter or a gift for friends who compulsively check transit apps. And for me, it scratched the itch to build something physical after drowning in software projects all year. 

So, here’s how I built it, what you could use, and some lessons learned. 

## The "Just Grab a Premade API" Setup

The simplest way to fetch transit data is to tap into public APIs. Many cities have developer-friendly platforms for real-time departures. For my build, I used the UK National Rail API, which is both free for small hobby projects and ridiculously reliable. If you're US-based, check out OneBusAway or TransitLand. 

The advantage? You get a firehose of live data out of the box, so if you want specific routes or nearby stations, it’s all there. 

Caveat? Some APIs (looking at you, New York MTA) are overcomplicated nightmares that make basic things unnecessarily hard. And while free tiers are great, rate limits can screw you if you tinker too much, leaving you staring at “429 Too Many Requests.” 

## Hardware: The Battle Between ESP32 vs Raspberry Pi Zero

This is where the side-project rabbit hole gets nasty. I went with an ESP32 board because it’s cheap ($9 for the legit version, or maybe $4 if you’re cool with Aliexpress knockoffs). It’s perfect for “set it and forget it” Wi-Fi setups. That said, a Raspberry Pi Zero (W) gives you extra breathing room.

- **ESP32 Pros:** Dirt cheap. Runs MicroPython. Small enough to embed in basically anything.
- **ESP32 Cons:** Tight memory (often 520 KB SRAM). If you need to parse janky XMLs or deal with odd API responses, it might choke. 

- **Raspberry Pi Zero W Pros:** More RAM, better Python ecosystem. Debugging feels like real coding, not “please run this serial command at 9600 baud.”
- **Raspberry Pi Zero W Cons:** Costs ~6x more ($30+) post-2023 since supply chains went ballistic. Also, I ran mine headless, and SSH-ing every time I needed to tweak something got old real fast.

If you’ve already got a spare Pi laying around, great. Otherwise, ESP32 is my default. 

## Display: E-Ink vs. LED Matrix vs. The Cop-Out (OLED)

Three popular choices here, with wildly different vibes:

1. **E-Ink** – Looks sooo premium. Also total overkill unless you’re designing for ultra-low power consumption. E-Ink refresh speeds are slow (like two seconds on cheap panels), and their Python libraries are frustratingly young. My first attempt failed completely because the library for my Waveshare panel didn’t play nice with ESP32. If you’re a Raspberry Pi believer, you’ll have better luck.

2. **LED Matrix** – This is what I settled on: an 8x32 ws2812b module. These are bright, cheap (~$15), and drive that retro “pixel art” aesthetic. Libraries like Adafruit’s NeoPixel make it dead simple, though the ESP32 hit about 70% memory capacity just displaying a scrolling train schedule. 

3. **OLED Cop-Out** – Basic 128x64 OLED modules are everywhere ($5). They lack pizzazz but work reliably. If your goal is “just show text in a box,” don’t overthink it. 

Not-so-fun fact: E-Ink’s driver setup added *two hours* to my project with zero payoff because I underestimated its library support. Don’t be me.

## Software Stack: Python, MQTT, and Flashing Nightmares

I built the project in MicroPython for ESP32, relying on umqtt.simple to push updates from my laptop to the board. Parsing live data is easier on the laptop (Node.js for the win), then publishing relevant info via MQTT, instead of overloading the ESP32 with logic. 

For home automation nerds: you can totally tie this into your existing Home Assistant setup. I didn’t do this because I like my side-projects separate from my smart-home chaos. But the Home Assistant community swears by it.

Biggest gotcha? Flashing MicroPython onto an ESP32 required me to downgrade esptool — **version 5.0 broke compatibility**, and it took an hour on GitHub forums to figure this out. Avoid that headache upfront. 

## So, Was It Worth It?

Honestly, yes. Sitting at my desk watching live train departures feels absurdly satisfying. Is it solving a problem in my life? Nope. But it’s fun — and there’s something deeply satisfying about building a “real-life widget” that isn’t just another browser tab. 

If you’re thinking of trying this: start small. ESP32 + OLED + a basic node.js script gets you 80% of the way without the hardware drama. If you’re braver, throw in the LED matrix or connect it to your Home Assistant.

Oh, and if you hate APIs, just scrape transit websites instead — Modern Transit on GitHub has open scrapers for most major systems. 

## FAQ

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Can I use this with Google Transit?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Google's transit data isn't open for this. However, many cities use GTFS (General Transit Feed Specification) that you can scrape via APIs like OneBusAway or locally hosted options like OpenTripPlanner."
      }
    },
    {
      "@type": "Question",
      "name": "What's the cheapest way to build this?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "ESP32 and a cheap OLED screen is the budget setup (~$15 total). Skip E-Ink unless you’re specifically building an always-on, ultra-low power version."
      }
    },
    {
      "@type": "Question",
      "name": "Can I make this portable?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, but powering it is the headache. A 18650 Li-Ion battery pack plus voltage regulator can keep it mobile for hours, but most ESP32 boards lack proper sleep mode unless you want to dig into custom firmware."
      }
    }
  ]
}
</script>
