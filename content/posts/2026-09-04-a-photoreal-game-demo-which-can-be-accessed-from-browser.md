---
title: 'Photoreal Game Demos in Your Browser: Unreal or Unrealistic?'
date: '2026-09-04 12:00:03+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: Browser-based photoreal game demos are here, but are they practical? We break
  down the tools and trade-offs.
---

Photorealistic game demos in the browser sound like magic, right? No installs, no updates, just click a link—and boom—your GPU hums to life. But the tech isn’t just wow… it’s specific. What’s the catch? What are people actually using? And is it something you should even consider building for your side hustle or game project?

Let’s break it down and weigh the contenders.

## WebAssembly + Unreal Engine: Shiny, Alien Tech

Unreal Engine is the king of "I can't believe this is real" graphics. With Unreal 5, specifically the Lumen and Nanite updates, photorealism isn’t just a buzzword—it’s the expectation. The wild part? Unreal demos can now run inside a browser using WebAssembly. 

Case in point: Epic's own city demo. Load it in Chrome or Firefox, and it’s like your laptop skips lunch and starts mainlining Red Bull. But this brings the first gotcha: performance scales wildly depending on hardware. In one r/sideproject comment, a dev mentioned their game shelled out 3 GB of RAM just to render a quiet forest scene—not even kidding.

The tooling here is amazing but heavy. Unreal/WebAssembly bundles can balloon to hundreds of megabytes. Forget about that aspiring player on Starbucks Wi-Fi... they’re noping out. Also, debugging WebAssembly isn’t for the faint of heart. Browser dev tools aren’t anywhere near as helpful as you'd hope. And platform limitations? Don’t try this on iOS Safari; it’s not gonna happen.

Good for: cutting-edge visuals, rattling GPUs, showing investors something flashy  
Not great for: casual web games, small-scale side projects, audiences with potato-tier hardware

## Three.js Blends Beauty with Practicality

Three.js is the old guard of browser-based 3D, but don’t call it old-fashioned unless you’re willing to eat your words. It’s a JavaScript library that strikes a balance between performance, size, and flexibility.

Sure, photorealism isn’t built-in—you’re not getting Unreal-level assets effortlessly dropped into your scene. But that’s the beauty of it. Combine it with glTF models or PBR (physically-based rendering) texture pipelines, and Three.js can deliver compelling visuals without frying your browser’s process tree.

What Three.js lacks in graphical wizardry, it makes up in size and approachability. Asset-wise, you’re looking at tens of MBs, not hundreds. Caching just works. No finicky WebAssembly magic, just raw JS doing its job. Plus, because it runs atop regular WebGL/WebGPU, it’s way more forgiving for older hardware. 

Downside: the dev time investment for true photorealism. r/sideproject threads are full of people debating optimal lighting and shader pipelines, and let’s just say it isn’t as out-of-the-box as Unreal. For one project, expect to spend weeks if you're tweaking reflections or fog effects.

Good for: stable browser performance, mainstream accessibility, "looks nice enough" creative flexibility  
Not great for: jaw-dropping realism with minimal effort  

## Gonna Mention Godot: Is It Worth It?

People love talking about Godot in these threads because it’s open-source and pretty feature-rich. But here’s the unsponsored truth: Godot’s HTML5 export has quirks that make it hard to recommend. 

First off, its rendering engine isn’t primed for the heavy photorealism Unreal thrives on. Think of Godot as a solid choice for stylized or low-poly games—but when you're aiming for that “is this real life?” effect, it falls short. One dev on r/sideproject straight-up called it “not ready for prime time” in 2024, and that’s mostly true as of writing in 2026.

There’s also bloat creep. Godot exports in the 60 MB range can spike depending on textures and shaders. This doesn’t look bad compared to Unreal, but it’s nowhere near the level of Three.js for lightweight web-first builds.

Good for: indie game devs experimenting with full-stack flexibility  
Not great for: bleeding-edge visual fidelity or browser-first workflows  

## The Real Question: Does This Even Make Sense for YOU?

I’ll just say it: browser-based photorealism is overkill for most indie devs. Sure, it’s flashy as hell to link someone a playable demo, but is it robbing you of development time that could improve the actual game? One second of UX frustration—lag, crashes, long downloads—and players bounce.

If you're targeting non-technical users, stick to Three.js. If you want wow-factor and have a legit team to support you, go Unreal + WebAssembly. And if low-poly whimsy is more your lane, sprinkle in some Godot. 

The browser space is incredible for what it can achieve, but you’ve got to factor in your audience’s frustrations just as much as their oohs and aahs.

### FAQ

#### Can Unreal Engine browser demos run on mobile?
Not reliably. Even if they “run,” WebAssembly + Unreal sprints your battery to death. Also, Safari can’t handle it. Stick to desktops for now.

#### How big are these demos’ file sizes?
Unreal can hit 300 MB easily. Three.js demos hover between 10–50 MB depending on assets. Godot HTML5 exports are around 60 MB barebones.

#### What hardware is needed for browser photorealism?
Expect users to have at least 8 GB of RAM and a semi-recent GPU. Anything older struggles to maintain smooth frame rates.
