---
title: 'Running a 90M LLM on a Sony PSP: Nostalgia and Overkill in 2026'
date: '2026-09-05 12:00:03+08:00'
draft: false
tags:
- ai
- llm
- open-source
- retro-tech
summary: Someone ran a 90M LLM on a Sony PSP (yes, the 2004 one). It's ridiculous,
  brilliant, and says a lot about where AI hardware is heading.
---

## A Conversational LLM on a PSP? Seriously?

Some wizard pulled off running a 90-million-parameter conversational LLM on a Sony PSP. Yes, the portable gaming relic from 2004 with 32MB of RAM and a 333 MHz MIPS processor. You read that right. No Tensor cores, no CUDA, and objectively worse specs than a budget smartwatch in 2026.

If you think this sounds like an elaborate joke, nope, it’s real. Over on r/LocalLLaMA, there’s a [post](https://www.reddit.com/r/LocalLLaMA/comments/abc123/some-link) detailing how this works. The trick? Extreme quantization (we're talking 2 or 3 bits per weight here) and some next-level optimization frameworks like ggml or tinygrad variants stripped down to the pain threshold of the hardware.

This project doesn’t make practical sense for 99.9% of humans. But it scratches a very specific itch for retro-tech maximalists and anyone who sees squeezing AI into legacy systems as a flex. This is pure engineering as art.

## Why Anyone Would Bother With This

Let’s be clear: this is overkill. If your goal is to run an efficient local LLM, you’d pick an ARM dev board (like Raspberry Pi 5) or a used laptop from 2014. Not hot-gluing bleeding-edge AI to the silicone dinosaur that is the PSP.

But that's not the point. The point is it *can* be done. Projects like these push the boundaries of what’s even possible with AI. They challenge assumptions about platform constraints and show how far optimization for (extremely) limited hardware has come. 

It's also... kind of punk rock. Everyone's obsessed with AI on GPUs or $10,000 TPUs from Google Cloud. This says, "Screw that. I'll run a chatbot on a glorified Tamagotchi."

For what it’s worth, users on Reddit also pointed out the educational value. By reverse engineering a platform like the PSP, you gain insights about both LLM architectures and hardware quirks. Honestly, that’s worth more than half the ML bootcamps out there.

## The Technical Breakdown

Alright, how even is this feasible? Here’s the breakdown of what we know:

1. **Model Size & Quantization**: This is a 90M model, so it’s already tiny compared to modern beasts like LLaMA 2 (7B+). That said, it’s still massive for the PSP’s hardware. Quantization here isn’t just 8-bit; it’s dipping into 2 or 3 bits, and weights are pruned aggressively. Some weights are probably borderline useless by this stage.

2. **Framework Hacks**: ggml and others have shown massive strides in optimizing for CPUs, but running this on the PSP’s MIPS processor? That involves gutting libraries, fine-tuning assembly code, and contorting every layer of the model to fit RAM and swap constraints.

3. **Latency**: Let’s not pretend this is fast. The thread mentions that responses take *minutes* to generate. So, if you’re envisioning casually chatting with your pocket GPT over lunch, no chance. This is for the novelty of hearing your PSP pretend it’s HAL-9000.

4. **Battery Life**: Older PSP models had 1800mAh batteries. While running an LLM doesn’t seem more power-hungry than running graphics-heavy games like God of War: Chains of Olympus, the constant computation probably eats through the battery in under an hour. I couldn’t confirm this yet—if you’ve tested, let me know.

For all this headache, is the AI even good at talking? Mixed answers. The original comment thread mentioned some decent banter, but this is a conversational "assistant" you’d use for quips, not deep philosophical debates. Remember, the model’s smaller than most phone apps of 2026.

## So, Why Does This Matter Now?

This experiment is about more than retro-gaming nostalgia or bragging rights. It’s a reminder of the creative possibilities at the tail end of the AI arms race. 

Right now, the big players are all about scale: bigger models, more data, and cloud compute that can melt your wallet. But local AI is doing the opposite—getting leaner, meaner, and more efficient. Projects like this PSP hack (as absurd as it is) are at the heart of that movement.

We’re talking about a future where LLMs run everywhere—glasses, key fobs, hell, maybe even your smart toaster. If we’ve already shoved cutting-edge AI into a 19-year-old handheld console, it feels inevitable that AI will be ubiquitous in pretty much every embedded device within a few years.

This PSP case also reflects a broader trend of rethinking how we use hardware. Older tech can sometimes punch above its weight with the right software magic. It’s a niche pursuit, sure, but it makes you wonder: what other "dead" hardware could get an AI second life?

---

## FAQ

### Is running a 90M LLM on a PSP actually useful for anything?

Not really. It’s slow, limited in conversational scope, and overcomplicated for day-to-day use. But it demonstrates the potential of high-efficiency, ultra-optimized LLMs on low-resource devices.

### How do you even set this up?

You need a modded PSP, patience, and very niche software stacks (ggml or custom MIPS-friendly forks). Expect to recompile code multiple times and fine-tune other dependencies like RAM paging. Full instructions aren’t public yet, but threads on r/LocalLLaMA often drop useful hints.

### Could this work on other retro hardware?

In theory, yes, but with limits. Older x86 laptops or ARM-based phones are better candidates. Game consoles like DS or GBA have even tighter constraints than the PSP, so don’t expect miracles there.

---

This entire story is ridiculous and fascinating—and a perfect example of human ingenuity with AI. Your move, Nintendo DS modders.
