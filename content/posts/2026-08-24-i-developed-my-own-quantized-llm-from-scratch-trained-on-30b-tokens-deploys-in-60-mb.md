---
title: 'From Scratch to 60MB: How I Built a Quantized LLM That Packs a Punch'
date: '2026-08-24T18:40:24+08:00'
draft: false
tags:
- ai
- llm
- open-source
- technology
summary: How I trained a compact, 60MB LLM on 30B tokens—and why it’s probably overkill
  for most people but still wildly fun.
---

If you’ve ever waded into r/LocalLLaMA, you know people love talking about squeezing every extra drop of performance out of their models. Smaller, faster, smarter—and bonus points if you use some fringe method nobody’s thought of yet. That was my whole vibe when I decided to roll my own quantized LLM from scratch. The result? A model trained on 30B tokens that deploys in just 60 MB. Was it worth it? Absolutely—for me. Should you try this? Let’s break it down.
## Why a Scratch-Built Model?
First, some context: this wasn’t my first rodeo. Like most, I started with finetuning off-the-shelf models (think LLaMA-2, GPT-J). But the itch for something bespoke hit hard after I saw posts like u/weirdcod3r’s breakdown of getting a 13B LLaMA running on an 8GB MacBook Air. If you don’t know the pain of staring at “CUDA out of memory” errors at 2 a.m., have you even trained a model?
Their approach—quantization wizardry, heavy preprocessing—worked wonders. But as soon as I read “60MB” somewhere else in the thread, my brain went, “Challenge accepted.” I wanted to see how far I could push "small, fast, useful." And yeah, it also scratched a massive ego itch.
## The 30-Second Version of How I Pulled It Off
I started with a tokenizer ripped straight from GPT-2 (because why reinvent wheel-shaped code). My dataset was a curated chunk of web text, books, and code—30B tokens in total. I leaned heavily on bitsandbytes for quantization, targeting 4-bit weights from the get-go. No pretrained checkpoints here; this was zero-to-hero JLLaMA (my totally original name).
The training pipeline was all Hugging Face 🤗 Transformers, with some secret sauce for dataset preprocessing. Think aggressive text cleaning, sentence splitting, and strict token filtering. Hugging Face’s `Trainer` handled the rest, chewing through a couple of $400 Hetzner servers (AMD EPYC, 512 GB RAM, A100s) over about three weeks. I could’ve done it on AWS but wow, their GPU instance prices are basically legal robbery.  
Final product: Quantized LLM using ~60MB storage, inferencing well within 1GB of RAM on CPUs. 
## But... Why 60MB, Really?
At this size, the model isn’t just portable; you can stuff it into a toy project, run it on a Raspberry Pi 5 (maybe—unverified, lmk), or deploy across hundreds of edge devices where memory constraints are non-negotiable. It’s not ChatGPT, but for concise replies, lightweight Q&A, or even chatbot experiments, it holds its own. Think of it as the AI analogue to a commuter bike—not sexy but practical as hell.
That said, I’ll admit: 60MB is overkill for most hobbyists. u/tinyMLmaker put it perfectly, "I just run QLoRA on [their] existing weights under 2GB and it already does 98% of what I need.” And yeah, that’s the real tradeoff. You lose versatility and speed of development for the sake of... what, being the guy with a 60MB LLM?
## How Does This Compare to Other Approaches?
Let’s make this practical. Here’s where this sits compared to more common lightweight LLM options:
### Off-the-Shelf Models with Quantization
LLaMA-2 or Falcon-7B can be quantized down via tools like GPTQ or AutoGPTQ. You’re looking at 2-8 GB for most purposes, disk size depending on parameter count and precision levels. These models are solid, community-tested, and endlessly tweakable. If you just want something that *works*—go here.
Gotcha? Huge baseline requirements for RAM and VRAM during finetuning. This is what pushed me toward my “let’s build small stuff from scratch” rabbit hole.
### Distilled Models (Alpaca, Pygmalion, etc.)
These are streamlined LLM variants trained on more specific use cases. An Alpaca variant tuned for instructions, distilled to 4-bit precision, can hit 2GB while outperforming yours truly’s on certain tasks. But distilled models are limited; they inherit quirks of their parent and bake in biases you might not want.
### My Approach
Raw, unadulterated control. If you’re a masochist who enjoys wrangling datasets and tweaking hyperparams, you get the challenge of tuning the entire architecture to your liking. The downside? Time costs. Money costs. Maintenance costs. You’re on your own, and debugging is hell. u/sadsandwich was right: “Training your own weights is 5% technical skill and 95% cry breaks.”
## Real-Life Performance: Does It Hold Up?
My 60MB LLM handles lightweight summarization in ~300ms on a Ryzen 7 CPU. Context window caps out at 1K tokens—plenty for small-form tasks but laughable for apps like code completion or long-form narrative generation. I tested it using fastapi for a local chatbot setup, and it works surprisingly well, as long as you keep expectations in check. Don't ask it to pontificate on quantum chromodynamics, though. Results are... amusing.
### FAQ
#### **Is this really practical for everyday users?**
Not really. This project is niche—one part learning opportunity and one part nerd flex. If you want ready-to-go models, stick with LLaMA-2 variants or Alpaca for now.
#### **How does it compare to GPT-3.5 or ChatGPT?**
Not even close. Those models are optimized and trained at a scale I can’t replicate in my garage. Mine is customized for specific tasks that don’t require massive context windows.
#### **What’s the best hardware for this project?**
I trained it on A100s, but you can run inference on a decent consumer-grade CPU like the Ryzen 5000 or 13th-gen Intel i7. Haven’t tested ARM yet—so if you pull it off on a Pi, let me know!
Done. This was a fun experiment, but the next project will probably involve something more practical—like an actually useful finetune.
