---
title: How One Developer Built a Fully Interactive 3D Ancient Egypt in 1–2MB Scenes
date: '2026-09-07 22:00:03+08:00'
draft: false
tags:
- indie-hacker
- web-dev
- 3D
summary: Building Ancient Egypt in 3D for the web in lightweight, ad-free, login-free
  glory. Lessons learned from 1,000+ hours of obsession.
---

If you’ve ever had the urge to recreate *entire civilizations* on the web but stopped because everything felt too heavy or clunky, this one’s for you. Over on r/sideproject, one dev (u/sandtablet) casually dropped that they spent over 1,000 hours building a fully interactive 3D reconstruction of Ancient Egypt. What makes it wild? The entire thing runs in about 1–2MB per scene. No ads, no logins, no cheap tricks.

Here’s what the r/sideproject hive mind thought about it—and what you can learn if you’re about to embark on a jaw-clenching deep dive of your own.

## Why 1–2MB Matters (And How They Pulled It Off)

You know what sucks? Waiting five seconds for a bloated website to load, only to be blasted with intrusive pop-ups. The creator, who clearly has a "data efficiency or die" mindset, squeezed Ancient Egypt into bite-sized chunks using techniques like precomputed texture atlases and aggressive mesh simplifications.

This isn’t just academic-level optimization. They’re pulling it off *in the real world*. One commenter (u/mellowramen) noted:  
> "*This is the first time I’ve seen something both historically detailed and performant on the web. It’s insane how fast it loads even on my phone.*"

Lesson here: If you’re building for broad audiences, keep it small. Web tech stacks like Babylon.js or Three.js have robust ecosystems, but even those can easily get bloated. Precomputing is your best friend if end-user experience matters.

## Babylon.js vs Three.js: The Community Is Split

Most of the heavy lifting for this project leans on Babylon.js, one of the two juggernauts of WebGL libraries. Naturally, this stirred some debate:

- **Babylon.js fans** argue it’s better for large scenes where you need advanced PBR materials and baked lightmaps. It’s also better documented than Three.js if you’re starting fresh.
  
- **Three.js enthusiasts** fired back that it’s more minimalist, so you can pick and choose the features needed without extra baggage.

The creator admitted that this trade-off wasn’t just technical—it was personal:  
> "*I started with Babylon because it’s what I knew from a VR side project last year. In hindsight, Three.js might have cut some weight, but I’m happy with how this runs.*"

Takeaway? Pick one and own it. This isn’t a CI/CD pipeline where you *must* nail efficiency—either tool will get you 90% there, so the time you spend learning is often your biggest cost.

## "But Who’s Hosting That?"

Ah yes, hosting. This came up a lot because squeezing files down to 2MB is one thing—serving them up reliably is another. The creator chose **Cloudflare Pages**, and people on the thread agreed it was a solid move. No cold starts, blazing-fast edge servers, and (for a static project like this) the free tier is probably all you need.

Alternatives? If you like self-hosting and have a bias against Big Tech™, other folks suggested **Vercel**, **Netlify**, or even classic **Hetzner cloud instances** if you’re okay rolling your own file watcher. The consensus, though, was overwhelmingly in favor of edge solutions like Cloudflare for this kind of read-heavy workload.

u/datasmol dunked on anyone overthinking it:  
> "*Bro, your files are 2MB. Your internet provider’s hamster could host that.*"

## Historical Accuracy vs. Creative Freedom

One underrated thing about this project? The creator didn’t hyper-focus on making it 100% museum-grade. Ancient Egypt is a tough subject—half the data feels like guesswork, and even credible sources contradict each other. Instead of spinning in research hell, they aimed for a mix of educational vibe and creative exploration.

> "*It’s historically *inspired*, not a PhD thesis. I wanted people to explore and imagine—not nitpick which temple column is where.*" 

This resonated with a lot of people who’ve had similar perfectionist bugbears sink their projects. Done > perfect.

## Should *You* Try This?

Rebuilding an entire historical setting in 3D is overkill—let’s not sugarcoat it. But you don’t have to aim as high to steal lessons from this. Here’s what stood out most from the r/sideproject feedback:

1. **Optimization isn’t optional anymore.** Everyone is tired of bloated downloads *and* slow sites, especially on mobile. Whether you’re doing fancy 3D or just simpler JavaScript apps, consider tools like Brotli for compression, or switch from PNGs to AVIF if you haven’t already.

2. **Let limitations guide your creativity.** Setting size limits (e.g., 2MB per scene) forces you to make smarter design decisions.

3. **Pick the learning project you’ll actually finish.** Let’s be real: u/sandtablet spent 1,000+ hours on this. Most of us don’t have that kind of time. Creating a single historically-inspired object or room (like a Pharaoh’s chamber) might be a better "small win" before scaling up.

---

### FAQ

#### How does this project handle mobile compatibility?  
The creator designed it mobile-first. By sticking to light asset sizes (1–2MB per scene), the performance on mobile is smooth, with little to no stutter. All interactions are touch-friendly using Babylon.js’s built-in support for mobile gestures.

#### Why choose Babylon.js over Three.js?
Babylon.js offers more advanced baked lighting options and tooling for large, fully interactive environments. However, Three.js can often result in smaller file sizes due to its modular approach. It depends on your needs and experience.

#### Can this kind of project scale for multiplayer?  
Not easily. The project is designed for single-user exploration rather than multiplayer sync. A proper multiplayer setup would require significant overhaul, especially to manage state and networking efficiently.
