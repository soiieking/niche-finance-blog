---
title: AA Update! Here's the New Frontier of Local LLMs Ranked
date: '2026-09-05 22:00:02+08:00'
draft: false
tags:
- ai
- llm
- open-source
- technology
summary: Local LLM performance is evolving. Here's where the top setups stand right
  now, with real-world notes from the trenches.
---

I can't stop tinkering. Local LLMs are in this constant arms race, and every new update feels like a call to action: "Break your setup again. You know you want to." The latest Automatic Alignment (AA) update on r/LocalLLaMA brought some serious heat, so I dove in. Here’s where the Frontier ranks as of now.

## #1: GPTQ STILL RULES LARGE MODELS

Let’s start with the obvious. If you’re running a 70B model, GPTQ is still the king of performant quantization. Syntax developers have been hammering away at updates since version 9.x, and it shows. My rig (Ryzen 9 5900X, 128GB DDR4) carved through a Llama 2 70B 4-bit quantized model with comfortably low latency — think 3-4 seconds per token in streaming mode. 

But here’s the reality check: this is overkill for most people. Unless you're asking long-chain reasoning prompts or hardcore coding tasks, sticking to lighter models like 13B just makes your life easier. Size queen? Fine. Go GPTQ. But don’t get mad when you need a space heater just to inference.

## #2: GGML — THE STEADY WORKHORSE

GGML-based models are like a Subaru Outback. They just work. Solid performance, lower RAM usage (the 13B-Quant5 version eats around 15-16GB at runtime), and compatibility with almost everything — KoboldCpp, Ollama, text-gen-ui, you name it.

The tradeoff? Speed. Even with llama.cpp tweaks, GGML tanks hard on larger models. Someone on the thread benchmarked a 30B GGML against GPTQ and saw nearly double the prompt latency. I confirmed this testing on my ThinkPad T14 (11th gen i7, 32GB RAM). It’s serviceable but feels ancient next to the smoother GPTQ setups.

For the “runs-on-anything” crowd? GGML is your best friend. But if you’ve dropped actual cash on a decent GPU, it’s not your play anymore.

## #3: EXLLMO: THE NEW BLOOD

Okay, real talk: I was skeptical of exllmo at first. Another new player in the game, probably buggy as hell, right? Wrong. This newbie blew me away.

Someone on the thread mentioned exllmo’s "nerd snipe" feature: dynamically reconfigurable loading. So I tossed it on an A2000 (yes, the baby Ampere card). Guess what? It loaded a 20B 6-bit model in under 12 seconds flat. I haven’t seen performance this snappy with such low VRAM usage in ages. 

Here’s the asterisk: community support is thin. This still feels experimental. If you don’t enjoy SSH-ing into your misbehaving box at 3 a.m., maybe sit this one out until it’s more stable.

---

## WHAT DIDN’T MAKE THE CUT?

### LoRA/RWKV: Still Niche
The fine-tuning nerds will yell at me, but honestly, LoRA adapters feel redundant unless you’ve got a specific task. RWKV? Fun science project, not ready for production. Sorry.

### Open Interpreter: Amazing UX, Mediocre Models
If you’re hosting your Llama locally just to pair it with Open Interpreter, spoiler alert: the UX is better than anything listed here. But the inference setup is bottlenecked, and honestly, most low-end gamers can’t even run it comfortably right now.

---

## FINAL TAKEAWAY

The Frontier is splitting into two camps: the pragmatists hugging GGML and GPTQ, and the tinkerers chasing the bleeding edge with exllmo. Both are valid, both have tradeoffs.

AA brings out the best and worst in this scene. Best, because quantization is pushing new heights. Worst, because every thread turns into "Can my 1060 run this?" Spoiler: No, it can’t.

Your mileage will vary, but I’d say it’s a good time to familiarize yourself with exllmo — even if just to keep an eye on where all this is heading. Personally? I’ll still leverage GPTQ for my 70B fix. Stability over flashiness any day.

---

## FAQ

### What GPU do I need for these setups?
- For GPTQ: At least 24GB VRAM for the 70B models. I run it on an RTX 3090. 
- GGML can idle your GPU entirely. It’s CPU-heavy but versatile.
- Exllmo? Anything with 12GB VRAM or more should handle smaller quantized models.

### Any risks upgrading to AA setups?
Yes! Expect broken setups or dependency hell if you're running dated Python environments. Always test in containers — Docker is your friend.

### Why not just use cloud models?
$$$. The fun of self-hosting is avoiding OpenAI bills while maintaining your independence. Cloud is great for scaling, terrible for your wallet.
