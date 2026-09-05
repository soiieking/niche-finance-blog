---
title: I Built a Virtual Aquarium—and You Can Fill It with Your Weirdest Fish
date: '2026-09-05 14:00:05+08:00'
draft: false
tags:
- indie-hacker
- creative-coding
- side-project
summary: What happens when you mix hand-drawn fish, a shared virtual space, and the
  internet's creativity? Probably chaos—and that's the point.
---

There’s something undeniably fun about building pointless things. Especially the kind that a small corner of the internet latches onto and makes weirder than you ever imagined. That's exactly what happened with my latest side project: a shared virtual aquarium where anyone can draw and add their own fish.

Yes, it’s ridiculous. But it works, people love it, and I learned a ton along the way.

## Why Build a Dumb Aquarium?

Because I could. Let’s start there.

This all started after falling down a YouTube rabbit hole about generative art. People were coding insane things—fractal landscapes, evolving creatures, all in real-time—but I wondered, what would happen if you let *everyone* participate? A canvas, but with a purpose. Something collaborative that could become its own little universe.

Enter: fish. Not 3D fish, not realistic fish. Hand-drawn, questionably-proportioned fish submitted by users. They swim around together in chaotic harmony. Some look anatomically correct. Most don’t.

In one day, I had hundreds of people adding fish. **The current MVP? It’s a simple web app built with Three.js for rendering, Firebase for real-time updates, and a drawing interface hacked together with Konva.js.** Low-budget, held together by duct tape, and honestly, that’s part of the charm.

## Tech Stack: Just Enough to Float

I started with Firebase for one simple reason: I didn’t feel like spinning up my own backend. **It’s free, real-time, and has decent SDKs, so why not?** It’s absolutely overkill for most tiny hobby projects, but for something collaborative like this, the database model clicked immediately.

Here’s the stack breakdown, for anyone curious:  
- **Frontend:** React + Three.js. Three’s GPU acceleration made adding physics (basic flocking/swimming) a lot smoother than I expected.  
- **Drawing interface:** Konva.js. It’s not perfect—it can feel clunky for mobile users—but it was easy to implement.   
- **Backend:** Firebase Realtime Database. Easiest way to sync fish positions without dealing with WebSockets directly.  

One Reddit commenter asked if I considered PeerJS for this instead of Firebase. Short answer: nope. The overhead didn’t seem worth it for a short-lived side project. If this ever explodes (unlikely), I might regret that.

## Why This Blew Up (and Why It Might Not Last)

Here’s the thing about weird side projects—the “rules” of building a startup don’t apply. Every indie dev on r/sideproject will tell you to validate your idea. I didn’t do that. I posted a demo, shared it for laughs, and boom, people just *showed up*.

### What worked?  
1. **Pure simplicity:** You don’t need to understand blockchain or sign up for an account. Open site → draw fish → done.  
2. **Shareable chaos:** Once someone spent 30 minutes lovingly crafting a photorealistic clownfish, another person drew an MS Paint blob, and some troll added a pixel monstrosity. That mix is *fun*.  
3. **Micro-communities love this stuff:** Redditors, digital artists, student Discords—this taps into that “weird internet” vibe people want to share.

But is it sustainable? Probably not. **Novelty projects tend to burn bright and fast unless constantly fed new features.** I’ve already had requests for better physics, aquatic plants, user accounts, and even ways to “breed” new fish species. That’s cool, but building all that for free? Hard pass.

## Lessons for Your Side Project Journey

If I sound gleeful about building this, it’s because I am. But let’s zoom out. For all the lessons I learned coding this, the most valuable takeaways weren’t technical.

1. **Release things unfinished.** I had a sketchy proof of concept and put it on the internet anyway. Guess what? No one cared about the bugs—they only saw the fun.  
2. **Not everything needs monetization.** Sure, I could slap ads on this, but why? The goal was to experiment, not get rich. Too many indie hackers lose momentum chasing business models instead of creative ideas.  
3. **Expect dumpster fires.** Someone always shows up to ruin the party. For me, it was someone spamming “fish” in the chat. A little moderation goes a long way.  

Am I going to scale this into SaaS-for-fish? No, but it’s staying up as long as my Firebase bill is cheap. It’s a reminder that side projects don’t need approval, traction, or even a reason. Sometimes “because I felt like it” is enough.

---

## FAQ

### How do you add your own fish?  
You hit the site, draw something (touch-friendly for mobile), and it instantly shows up in the tank. There’s no accounts, no gatekeeping. Just pure collaborative fun.  

### How much did this cost to build?  
The backend is free thanks to Firebase’s limits (so far). Frontend hosting runs nearly nothing on Vercel. The biggest investment? My time. Overall, <$10.  

### Can my fish die or get eaten?  
Nope. This isn’t *Spore*. The current version is strictly peaceful—fish just swim, vibe, and occasionally clip through each other.
