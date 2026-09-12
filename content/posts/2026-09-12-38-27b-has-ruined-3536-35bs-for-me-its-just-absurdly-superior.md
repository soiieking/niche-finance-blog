---
title: Why 3.8-27B Makes 3.5-35B Look Like a Togglable Light Theme
date: '2026-09-12 22:00:04+08:00'
draft: false
tags:
- ai
- llm
- open-source
- technology
summary: A dead-simple guide to running 3.8-27B and why you'll never go back to 3.5-35B
  after this.
---

## 3.8-27B: Absurd Performance, Real Usability

Look, I didn’t think I’d care. I skipped 3.6 entirely because 3.5-35B was so good. It felt stable, predictable, and didn’t set my GPU on fire. Then came 3.8-27B, and within about ten queries, it was obvious: this thing *obliterates* 3.5/3.6-35B, to the point where going back feels like switching from fibbing GPT-3 to GPT-4. The jump is that big.

But it’s also heavier, fussier, and not for every use case. Here's why you might want it, how to set it up, and what the Reddit hive-mind is saying.

---

## Why You Want 3.8-27B

The upgrade isn’t just about accuracy. It’s the *confidence* this model exudes. Responses are tighter, don’t meander as much, and the “hallucination rate” (yes, I know this is fuzzy to measure) feels cut in half. I can't tell you how many comparisons I’ve seen where 3.5 bungles nuanced coding tasks or fuzzy logic, and 3.8 just *lands it*.

Examples from r/LocalLLaMA have been everywhere: math-heavy JSON parsing, tricky Python edge cases, even detailed ethical hypotheticals. u/quantum_lad posted an example this week comparing an SQL optimization query across both models, and 3.8 basically rewrote the query like it *knew* the database structure. 3.5? Something about indexes that didn’t even exist.

Here’s the catch: this is overkill for a ton of people. If most queries you throw are like “write me a regex to extract emails,” 3.5-35B will crush. Move to fine-tuning (or heavier roles), though, and it’s not even close.

---

## What You Need to Run This

Before you get hyped, your hardware matters here. 3.8-27B is a beast and will laugh in your face if you try it with anything less than serious power.

### Minimum Recommended Setup:
- **GPU**: At least 2x 24GB cards (think RTX 3090 minimum). For single GPU setups, 48GB cards like the RTX A6000 can *barely* handle it. Dual 3090s scale much better though.
- **RAM**: 32GB system memory *at minimum*. Swapping will hurt a lot if you’re cheaping out here.
- **Disk**: Ideally NVMe. HuggingFace downloads get chunky, and swapping via SATA SSD will chug.
- **Framework**: Exllama is still king for speed. You *could* use GGML, but the performance hit isn’t really worth it unless you’re running on CPUs alone.

For dual GPUs, you’ll want some straightforward configuration tuning. Multi-instance setups (e.g., `CUDA_VISIBLE_DEVICES`) will wreck your day if you try ignoring shard allocation errors.

---

## Setting up 3.8-27B on Exllama

Assuming you’ve already got Python and CUDA set up, here’s the workflow with Exllama.

### Step 1: Clone Exllama  
Open a terminal and grab the latest version:

```bash
git clone https://github.com/turboderp/exllama
cd exllama
pip install -r requirements.txt
```

### Step 2: Download the Model  
Grab the 3.8-27B weight file from HuggingFace. I like using `aria2c` personally because it’s way faster than `wget` for big pulls.

```bash
aria2c https://huggingface.co/models/3.8-27B.safetensor --header "Authorization: Bearer YOUR_HF_TOKEN"
mv 3.8-27B.safetensor models/
```

Replace `YOUR_HF_TOKEN` with your HuggingFace token. If you're downloading GGML quantizations instead, adjust paths and file names accordingly.

### Step 3: Tweak Exllama Config  
Edit the `turboconfig.json` (or whatever config file format you love most). For dual GPUs:

```json
{
  "max_memory": "15 GiB / 15 GiB",
  "shard_worker_allocation": {
    "gpu_1": 0.6,
    "gpu_2": 0.4
  }
}
```

You’ll need to balance this based on your cards’ VRAM. Trial and error here helps.

### Step 4: Fire Up Inference  
Let’s test the installation real quick:

```bash
python inference.py --model-path models/3.8-27B/
```

Basic prompt? Sure. Ask it something like, “What’s the derivative of sin(x) to the power of x?” Just don’t blame me when it explains the chain rule *and* plausibly guesses why you asked.

---

## 3.5 vs 3.8: The Verdict

If you can afford the hardware or already own it, there’s no reason to stay on 3.5/3.6-era models. The capability jump is insane. That said, 3.8-27B is *not* for casual setups. Running it locally on weak hardware is either impossible or a lag-fest. 

There’s a fair argument to say “just rent a box on Vast.ai” instead of attempting this locally. A 2x A100 instance there costs roughly $1.80/hour as of last week, which is great if you just need to process something heavy for a few hours.

But for those with strong cards at home? It's worth it, full stop.

---

## FAQ

### Do I need dual GPUs to run 3.8-27B?
Technically, no. Single GPU cards with high VRAM (48GB+) *can* run this, but the experience is smoother on dual setups. You’ll lose speed on anything subpar.

### What quantization method works best here?
Exllama really shines with 4-bit quantization for 3.8-27B. Anything lower, like 3-bit GGML runs, introduces serious accuracy drops.

### Is 3.8 ready for fine-tuning?
It’s not super battle-tested for fine-tuning yet. A lot of Reddit users, like u/mathwhiz42, are running experiments on PEFT adapters. Mixed results so far, so proceed with caution.
