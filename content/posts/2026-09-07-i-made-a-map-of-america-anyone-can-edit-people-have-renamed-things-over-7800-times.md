---
title: I Made a Map Anyone Can Edit — 7,800 Renames Later, Here's What Happened
date: '2026-09-07 08:00:05+08:00'
draft: false
tags:
- indie-hacker
- sideproject
- mapping
summary: How a crowdsourced map spiraled into 7,800+ edits. Renamed highways, oversharing
  locals, and lessons from r/sideproject.
---

## What Happens When the Internet Renames America

So, someone on r/sideproject made a map of the US where *anyone* can rename places. It’s as chaotic (and hilarious) as you’d imagine. To date, over **7,800 edits** have been made — from swapping "Los Angeles" to "Traffic Hellhole" to more wholesome upgrades like renaming tiny towns after pets. It's crowdsourcing with zero brakes.

But with 7,800+ edits, the big question is: What's the point? Novelty? Local ego? Or are we watching decentralized cartography unironically try to improve the world?

Here’s a breakdown of the best insights from the r/sideproject thread, mixed with some hard-earned lessons in letting the internet touch your side project.

---

## 1. People Love (and Abuse) Freedom

This map — originally a low-key personal side project — became a chaotic experiment in digital anarchy. As one Redditor, u/byte_me_42, put it: _"Give the internet a tool and they'll either optimize it beautifully or ruin it for laughs."_ Both happened.

Highlights from the thread include renaming Yellowstone National Park to "Bear Disneyland," and Mount Rushmore became "Four Old Dudes." Not exactly National Geographic-worthy, but undeniably entertaining.

### Chaos Comes Cheap  
The backend is as minimal as the project is wild. According to the creator, it runs on **Leaflet.js**, **Supabase**, and **a dirt-cheap $5 Vultr server** (remember kids: Hetzner is great, but no US data center). Combined, this stack handled thousands of edits **without hitting the rate limits**. That’s solid considering the hit-and-miss nature of free crowdsourcing.

---

## 2. There’s Power in Micro-Stories

Not every rename was meme-worthy. Some were deeply personal. One user edited their small town to reflect a local inside joke. Another renamed a creek behind their childhood home because the original name was _"off by half a mile."_ This isn’t just trolling; it’s local lore preservation.

As u/JavaDad123 noted, _"Maps are weirdly emotional. People see themselves in these places."_ True. Google Maps might lead us from A to B, but projects like this remind us that locales also have character. Street names, landmarks, even "the spot with the good tacos." They're all part of a collective memory nobody asked Google to edit.

---

## 3. Moderation: Necessary Evil or Total Fun Killer?

Of course, open editing has its caveats. You get real gems like "Breadtown, USA," but also NSFW renames that can’t be printed here. The project creator admitted in the thread that **95% of edits stick** (vandalism isn’t as bad as you’d expect), but the 5%? Absolute garbage.

One suggestion came from u/cli_noob: _"What about putting changes up for a vote?"_ Clever in theory, but the project owner shot back with valid hesitation: "Voting opens up a whole new can of worms for bots or Reddit brigading." Fair. A self-policing system might make sense here, but that’s overkill for a fun side project. Let the locals sort it out.

---

## 4. Where Are We Going With This?

Honestly? No one knows yet. The way u/Mapping_Maniac framed it: _"This is digital graffiti on top of GIS data."_ It’s cool and chaotic but maybe not _useful_. Still, that’s not really the point.

The entire project feels like a love letter to the untapped ways maps could evolve beyond utility. Sure, Google won’t be licensing “Breadtown” anytime soon, but proof-of-concepts are valuable. Could you imagine micro-local maps tied to specific communities instead of corporations? This could be the indie MySpace of geography. Someone’s gonna figure it out.

---

## FAQ (Naturally)

### **How do I submit edits to the map?**
The process is wide open right now. You can literally head to the live site, zoom in, and rename spots with zero account needed. Just don’t be a jerk. 

### **Is there a way to revert troll edits?**
Yes, the database logs cover every single change, and rollbacks are done via admin rights. But again, the creator mentioned that most edits are left untouched unless they go off the rails.

### **What’s the tech stack?**
As mentioned earlier, it’s **Leaflet.js** on the client, **Supabase** for the database, and **Vultr** handling the server side. Lightweight, scalable, affordable. Simple but surprisingly durable against Reddit-level chaos.

---

This whole project highlights an underrated truth: side projects don’t need a grand purpose. Sometimes, it’s enough to build something quirky and let the internet figure out why it matters.
