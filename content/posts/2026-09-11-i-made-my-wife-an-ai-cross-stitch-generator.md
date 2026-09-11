---
title: I Made My Wife an AI Cross Stitch Generator (And Why You Might Want One Too)
date: '2026-09-11 10:00:04+08:00'
draft: false
tags:
- indie-hacker
- ai-tools
- side-project
summary: Why someone built an AI-powered cross stitch generator for their spouse,
  and how this experiment reflects the overlap of tech and hobbies.
---

So, someone on r/sideproject made an AI cross stitch generator for their wife. On the surface, it’s a cute one-off hobby project. But peel back a layer, and this touches on a bunch of topics that are super relevant right now: how AI seeps into niche hobbies, the role of DIY tools for non-developers, and how side projects evolve when they’re made for *someone specific* instead of a general audience.

Here’s what went down and why this matters (even if you’d never touch embroidery).

## The Origin Story: "My wife said pixel art was annoying"

The original poster (OP) mentioned that cross stitch patterns are essentially grids of pixel art—except every stitch is a color you’d need to translate into embroidery floss codes, like DMC’s color chart. This process can turn surprisingly tedious and manual. You design a pattern, then spend hours Googling floss numbers or tweaking Excel. It’s 2026. Nobody has time for that.

So, OP built an AI generator that takes normal, everyday images (think: your pet’s face or your favorite meme) and spits out cross stitch-ready patterns—including proper colors mapped to embroidery floss codes. They used Python, OpenCV for image processing, and a lightweight Hugging Face model for color matching. Total dev time? About 20 hours across a few weekends, according to their post. They even demoed it working on a $5 Linode instance, hitting under 500 MB RAM usage during inference. Pretty lean.

Their wife loved it. Reddit, naturally, did too.

## Why This Matters: Niche Hobbies Meet Casual AI

Here’s why this isn’t just a throwaway project. Tools like this signal a shift in AI applications: from general-purpose chatbots to ridiculously specific apps for hobbies and passions. And let’s be real—niche hobbies are *perfect* for AI. They’re repetitive, often involve tedious manual work, and benefit hugely from pattern recognition.

Cross stitch is perfect AI fodder because:

1. The input/output is constrained. The AI doesn’t need sentience or complex reasoning. It’s "image-in, grid-out."  
2. Mistakes aren’t catastrophic. A misidentified color code won’t break the hobby; it’s just annoying.  
3. There's no serious commercial competition. Try Googling "cross stitch generator"—you’ll find clunky free tools stuck in 2011 UX hell.  

So this is exactly the kind of project a solo dev can tackle without having to deal with massive competitors or legal hell (looking at you, AI-generated art lawsuits).

## Could This Go Commercial?

Probably. Several Redditors chimed in asking if OP would release it as a web service or app. One suggested a $10/month subscription model, but OP said it "feels weird charging for something I made for personal use." Gotta love that indie hacker guilt.

That said, there’s precedent here. A few years back, [Pattern Keeper](https://patternkeeper.app/), a paid app for managing cross stitch patterns, exploded in the embroidery community—and it’s still going strong at $9.99. It doesn’t do AI, but it streamlines workflows. People *will* pay for convenience in hobbies they love, especially if you skip the sleaze of subscriptions and overpriced DLC.

If OP wanted to go pro, they’d need better UX (current setup is CLI-based), a web front-end, and maybe an API for color chart customizations (not everyone uses DMC floss). All doable, but it’s a grind—and OP doesn’t seem interested. Mad respect for keeping it low-key.

## Why Most Side Projects Fail (and This Didn’t)

Let’s zoom out a bit. This project worked because it started with one super clear audience: OP’s wife. A lot of side projects fail because they aim at vague "users" or try appealing to Reddit at large. “Scratching your own itch” only works if the itch *actually exists for someone right now*—not a hypothetical someone.

This also didn’t try to do too much. It’s not automating embroidery machines or spinning up massive GANs to invent Insta-worthy patterns. It solved a single, highly specific pain point well. That’s it. And that’s often *enough.*

## Could You Make Something Like This?

Good news: you don’t need to be OpenAI to hack together something like this. OP’s entire stack (~500 MB memory, Python, OpenCV, Hugging Face) is beginner-accessible with a few tutorials. Even if you’re not a dev, tools like RunPod or Replicate simplify deploying small-scale AI apps. Costs start as low as $0.10/hour if you host sparingly. 

The tricky part is figuring out *what hobby problem you want to solve*. People undervalue working within constraints, but side projects thrive inside narrow lanes like this. Once you widen the scope too much, you’re doomed.

### TL;DR

Someone built an AI cross stitch generator for their wife. It’s not life-changing tech, but it’s a perfect example of AI slicing into niche hobbies. This matters because:  
1. Niche + AI = sweet spot for indie devs.  
2. Small tools solve real problems, no VC bloat required.  
3. Doing something personal gets you further than chasing Reddit upvotes.  

The real takeaway? Build for a person, not "users." 

---

### FAQ

#### What exactly does the generator output?  
It creates a grid-based pattern with exact color codes (DMC floss numbers) for each pixel, formatted for cross stitch. You just load an image—like a pet’s photo—and it handles resizing, gridding, and color matching automatically.

#### How much did it cost to build?  
OP’s dev time aside, they ran everything on a $5/month Linode instance during testing. If you don’t DIY and want scalable hosting, expect to pay around $10-$30/month for tools like Render.

#### Could this work for other crafts?  
Probably! Similar grids + color matching are used in beadwork (Perler/Hama beads), quilting, and even LEGO mosaics. It’d take some tweaks but isn’t conceptually hard. AI + hobbies is barely tapped territory.
