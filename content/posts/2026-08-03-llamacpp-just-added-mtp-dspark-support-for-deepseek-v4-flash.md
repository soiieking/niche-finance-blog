---
title: "llama.cpp MTP Support for DeepSeek V4 Flash is a VRAM Bloodbath (But Worth It)"
date: 2026-08-03T02:30:06+08:00
draft: false
tags: ["ai", "llm", "open-source", "technology"]
summary: "llama.cpp just landed MTP / DSpark support for DeepSeek V4 Flash. I spent the weekend breaking my rig to test it."
---

I already know what I'm doing this weekend. llama.cpp just merged Multi-Token Prediction (MTP) and DSpark compatibility for DeepSeek V4 Flash, and the r/LocalLLaMA thread is absolute chaos. 

I pulled the latest `b4123` build the second I saw the PR. 

My initial reaction? It's fast. Aggressively fast. But it is a complete VRAM bloodbath. I ran it on my main rig with four RTX 3090s (96GB VRAM total), and I barely squeezed in a decent context window. More on that in a second.

### MTP actually works, but the math is brutal

If you aren't knee-deep in the architecture, MTP means the model predicts multiple future tokens in a single forward pass. DeepSeek V4 Flash uses this to push out roughly 180 tokens per second on my setup. To put that in perspective, standard autoregressive generation on the same card nets about 65 t/s.

But MTP requires keeping speculative token trees in memory. That means a massive KV cache footprint.

I loaded the Q4_K_M quant of V4 Flash. Bare metal, vanilla llama.cpp, standard greedy decoding. The base model weights sat at 38GB. The moment I kicked on MTP with a 32k context, VRAM spiked by another 28GB. 

If you're running dual 3090s or a single 4090, don't bother. You're going to spill straight into system RAM relying on PCIe 4.0, and your speed will tank to single-digit t/s. The overhead literally negates the speedup.

### The Docker vs Podman debate
Skip the containers for this specific build. 

I usually run everything in Docker on my Hetzner box, but I wasted two hours fighting GPU passthrough issues on Podman when I first tested this. Standard Docker with the NVIDIA Container Toolkit worked fine, but honestly, for bleeding-edge GGUF merges like this, bare metal is your best friend. One Redditor in the thread (shoutout to u/quant_queen) pointed out that compiling natively on Ubuntu 24.04 with `cmake -B build -DGGML_CUDA=ON -DFASTMTP=ON` skips the wrapper overhead entirely. They were right. Bare metal gave me a 12% speed bump over the Docker image. 

### What about cloud? Hetzner vs RunPod
If you don't have a 4 GPU弗兰machine at home, you're looking at cloud. 

I fired up a RunPod instance with 8x A100 80GB to see how the big bois handle DSpark's MTP. Total cost: $11.80 an hour. It chewed through the context allocation in seconds and pushed over 300 t/s. It was glorious. It was also deeply irresponsible for a Saturday afternoon hobby test. 

DigitalOcean is a joke for this stuff. They don't even have the inventory. If you want to test this without selling a kidney, Hetzner's GPU auction is the only sane play. I grabbed an RTX 4090 auction instance last month for €0.51/hr. It took three days to provision, but it's the only remotely affordable way to test high-VRAM quants. 

### The community is split

The r/LocalLLaMA comments are a mess right now. Half the users are saying MTP is a parlor trick that degrades output quality. The other half are treating it like the holy grail of local AI. 

I haven't tested this enough to have a strong opinion on catastrophic loss. From my 50 or so prompts, the output seems perfectly coherent. I haven't checked it on ARM either, so Mac Studio users, your mileage may vary. Apple's unified memory pool is amazing until you have to compute token trees across a 512-bit bus. It might stall. Dig into the `llama-bench` logs before you commit.

### So, is it worth the hassle?

Right now? No. It's overkill for most people. 

The memory overhead is just too punishing for consumer hardware. If you are on a consistent 24GB or 48GB setup, just stick to the baseline V4 Flash AUTOREGRESS build we had last week. It's rock solid. MTP is strictly in "alpha hacker" territory. It's a fantastic technical achievement, but unless you have 80GB+ of VRAM sitting idle, wait for the memory optimization PRs to land. 

I'm going to keep torturing my server, but I'll probably roll back my daily driver to standard inference tomorrow. My rig sounds like a jet engine and my office is currently 90 degrees.