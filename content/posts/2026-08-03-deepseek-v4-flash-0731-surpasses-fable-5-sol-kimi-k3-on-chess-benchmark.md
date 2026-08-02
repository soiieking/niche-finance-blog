---
title: "DeepSeek-V4-Flash-0731 Beats Fable-5 and Kimi-K3 on Chess, and Nobody Cares"
date: 2026-08-03T04:32:07+08:00
draft: false
tags: ["ai", "llm", "open-source", "technology"]
summary: "DeepSeek-V4-Flash-0731 just crushed Fable-5 and Kimi-K3 on the Chess Benchmark. Here is why that does not matter."
---

I saw the r/LocalLLaMA thread this morning. Everyone is losing their minds because DeepSeek-V4-Flash-0731 supposedly surpassed Fable-5, Sol, and Kimi-K3 on the latest Chess Benchmark. A new state-of-the-art open-weight model beating the closed API titans. On paper, it is a massive win.

In reality, the benchmark is completely cooked.

Don't get me wrong. I run DeepSeek on my homelab exclusively. I was one of the first idiots to try shoving DeepSeek-V3 onto a Mac Studio when the MLX conversions broke every five minutes. I love these models. But if you actually look at how these models play chess, you realize they are just regurgitating a massive grandmaster game transcript. 

They are not calculating. They are doing pattern-matching recall from the training data.

### The Benchmark Illusion

User u/checkmate_bro covered exactly what I experienced when I ran the eval myself. DeepSeek-V4-Flash-0731 plays the Ruy Lopez magnificently. It plays the Sicilian decently. But try steering it into a weird variant or botching the opening on purpose to test spatial reasoning. It completely collapses. It will try to castle out of check, or move its king three squares sideways and insist it is a legal capture.

If a human grandmaster played like this, you would call an ambulance. The model just memorized the PGN files of every game played since 1850. 

Fable-5 got dragged in the thread for hallucinating a knight move on turn 14, but at least its contextual reasoning during mid-board scrambling was somewhat coherent. Kimi-K3 just stalls out and tokes endlessly until it hits the context limit and crashes. Sol crashed my Ollama instance twice. 

### Hardware and Realities

I ran the 236B FLA-quant of V4-Flash on a rented Hetzner bare-metal box (dual RTX 4090s, €1.13 an hour) to test the 140-token logical chains it supposedly uses to plan attacks. Output felt fast—about 35 tokens a second—but the VRAM overhead was absolutely brutal. 

Shoving all that sparse routing into the VRAM took 38GB. If you are trying to host this locally on a Mac with unified memory, good luck. The MLX conversion is still a work in progress, and the swap thrashing will absolutely destroy your SSD. Your mileage may vary, but I haven't tested this on ARM yet because I like my RAM chips not on fire.

### Does Anyone Actually Care?

This is overkill for most people. Seriously. Who is using local LLMs to play chess? Stockfish is 100MB, takes 4 seconds to set up on a Raspberry Pi, and will beat the brakes off any LLM on the planet while using 0.1% of the compute. I love open-source AI, but we are optimizing for a use case that literally nobody uses.

The thread is split down the middle. Half the sub thinks this is proof that sparse MoE architectures might someday unlock AGI via multi-step logical planning. The other half thinks we are just burning diesel waiting for a collision.

I fall in the latter camp. V4-Flash-0731 is a phenomenal coding assistant and a great local chatbot. Kicking its tires on the Chess Benchmark is like buying a Ferrari to drive to the end of your driveway to check the mail. Sure, the engine sounds cool, but it does not prove anything we didn't already know.