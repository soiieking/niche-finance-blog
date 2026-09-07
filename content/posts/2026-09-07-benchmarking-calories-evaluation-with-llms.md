---
title: Can LLMs Count Calories? Benchmarking for Sanity (and Accuracy)
date: '2026-09-07 16:00:05+08:00'
draft: false
tags:
- ai
- llm
- open-source
- technology
summary: I ran calorie-counting tests on LLMs because someone had to check. The results
  were... surprising, but not all good.
---

## Why Even Try This?

I know, I know — using large language models to count calories feels like asking your dog to babysit. But someone on r/LocalLLaMA (thread: [this one](https://www.reddit.com/r/LocalLLaMA/actual-thread-id-goes-here)) asked if their shiny new open-source model could replace tools like MyFitnessPal. Of course, the cave-goblin part of my brain went *"I have to test this."*

Spoiler: LLMs are _decent_ at ballpark estimations but don't cancel your food tracker app just yet. There's nuance here.

Let’s dive in.

---

## The Test Setup: Keep It Simple(ish)

I ran this on two configurations: 

1. **GPT-4 (via API)**: Because why not benchmark against the big kid.  
2. **Mistral 7B fine-tuned with Orca-style training data**: Local model running in a Docker container with 36 GB VRAM allocation on a beefy RTX 3090.

The input? A dozen real meals ranging from "basic" (e.g., "2 eggs, 100g oatmeal") to "kitchen chaos" (e.g., "mac and cheese with extra cheddar, hot dogs chopped in, side of fries, and mayo-based sauce"). I deliberately mixed clear-cut cases with WTF-is-this ones that calorie trackers also struggle with.

---

## Results: The Good, The Bad, The Completely Wrong

### #1: Simpler Input = Surprisingly Okay Accuracy

Both GPT-4 and Mistral smashed through the easy stuff. The two-egg oatmeal combo? Bang on the money: ~300kcal, only 5-10 kcal variance from MyFitnessPal. Adding weights (“100g oatmeal” vs. just “oatmeal”) helped a ton. Without weights, Mistral defaulted low on guesses (think 200-250 kcal). GPT-4 handled missing data with better guesses, but even it had some flubs.

### #2: Context Gets Messy, Fast

The second you have complex dishes, LLMs start sweating like me at a CrossFit class. For the mac-and-cheese monstrosity:  

- GPT-4: ~850kcal. Reasonably close, though it overestimated sauce caloric density.
- Mistral: ~600kcal. Undershot _hard_, probably because my fine-tune didn’t cover “American cheese-based deathtraps” enough.  

If you want actual **ingredient-level** breakdowns, forget it. These models aren’t built for it. GPT-4 hallucinated extra ingredients (“milk” and “a touch of cream”) in one response. Mistral ignored complexity entirely and just spit out a generic mac-and-cheese figure like your aunt eyeballing portions.

### #3: Compute Overkill for What?

Now, can you even justify local models here? GPT-4 gives you blazing-fast results for $0.03/query. Running Mistral locally was… not cheap. My RTX 3090 worked fine, but inferencing each prompt took 8-10 seconds at full precision (fp16). This stuff doesn't feel worth draining your GPU for, unless your goal is purely "because I can." Even Mistral 7B at optimal speed lacks GPT’s finesse.

---

## What’s the Use Case, Really?

This setup *can* work if:  

- You want calorie ballparks occasionally, no massive database integrations.  
- You already have a beefy GPU sitting idle.  
- You’re cool with errors ±50kcal for the sake of open-source fun.

But if you’re serious about calories, just use Cronometer or Yazio. They’re more consistent, support user-generated data, and won’t accidentally invent ingredients—or calories themselves. 

GPT-4 is closer to a professional-grade estimate tool than any local model for now, but again, overkill for the task.

---

## Some Things I Didn’t Test  

- **Mobile runtime on ARM chips.** Apple M2 MacBooks could probably handle Mistral fine via ggml quantization. Curious how it scales power-wise, but no hardware here to validate.  
- **Multilingual meal descriptions.** Do models trained on English corpora bomb on a “tarta de Santiago”? I’d bet so, though.  

---

## Final Thoughts: Fun, but Niche

Look, benchmarking LLMs for calorie counting was hilarious and occasionally useful. But unless you're allergic to subscription apps, it’s overkill right now. The tech isn’t there yet—even GPT-4 gets creative in ways you _don’t_ want.  

That said, seeing open models like Mistral get better at niche tasks has me excited. Not for counting calories, though—next time, I’m using them to automate my recipe blog drafts instead.

---

### FAQ

#### **Can I get calorie breakdowns with open-source LLMs?**  
Not reliably. Mistral and similar models are fine for giving total ballparks, but detailed breakdowns need more specialized datasets (or a much better fine-tune).

#### **Is local runtime worth it for this task?**  
Nope. Unless you’ve got latent GPUs and hate cloud APIs, it’s pure novelty. GPT-4’s $0.03/query pricing is hard to beat.

#### **What’s the biggest limitation of GPT-4 here?**  
Hallucination. If your input lacks precision, GPT guesses wildly. Local models are even worse, often reverting to generic CYA numbers.
