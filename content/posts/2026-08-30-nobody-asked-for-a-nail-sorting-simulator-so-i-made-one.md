---
title: Nobody Asked for a Nail Sorting Simulator. So I Made One Anyway.
date: '2026-08-30T08:00:48+08:00'
draft: false
tags:
- side-projects
- indie-hacker
- absurd-tech
summary: A nail sorting simulator no one asked for, yet somehow it exists — here's
  why I made one and what I learned along the way.
---

## Why Even Build This?
No one was clamoring for a nail sorting simulator, but here I am, delivering what precisely zero people wanted. This idea came from a throwaway comment on r/sideproject — someone joked about how no one should make a game like that. Naturally, I thought, "Challenge accepted." Let's call it a mildly chaotic social experiment. Or just me avoiding my actual paid work.
Let me clarify: this is not a polished game for Steam. It's a browser-based mess where you're literally sorting virtual nails into bins for size, shape, and maybe a random "rustiness" metric. It’s oddly meditative. And stupid. Both can be true.
## The Build Process: Overkill? Probably.
I decided to go for a vanilla HTML/JS build, keeping it light and portable. There is no React, no Ruby on Rails backend, none of the usual heavy machinery. Why? Because overengineering is a real disease in the indie dev world (and yes, I've been guilty of it). I wanted this to load instantly, even on a 2008 ThinkPad running Firefox.
**The tech stack:**
- **HTML/CSS/JS:** Because it’s unironically fine for small projects.
- **Vite for bundling:** A bit of overkill, but faster builds helped when I iterated on the UI.
- **GSAP:** For animations. Sorting nails is boring unless it looks buttery smooth.
The whole thing sits at about **17.2 KB gzipped**. For context, this is smaller than the average Wordle clone, which feels like an appropriate flex.
## What Works
### 1. The "Game" Loop
Surprisingly, sorting nails is kind of satisfying if you get the animations and sound feedback right. I leaned into the "ASMR-adjacent" vibe, adding subtle clicks when the nails land in bins. Think of it as the lovechild of Bejeweled and a hardware store.
### 2. It's Weirdly Sticky
No, it hasn’t gone viral (thank god). But roughly 1 in 5 people I send it to waste at least 10 minutes playing, which is **far beyond my expectations**. A commenter on r/sideproject played for 18 minutes straight and said, "Why the hell am I still doing this?" Success, I guess?
### 3. Minimal Setup Time
One of my personal gripes with modern side projects: deployment nightmares. The perfectionist in me has fought with Kubernetes more times than I care to admit. This time, I kept it simple. The whole thing runs on **Netlify’s free tier**, and my total deployment time was maybe 15 minutes, including DNS tweaks. This isn't a game-changer, but it's a stress-saver.
## What Sucks
### 1. There's No Endgame
Sorting nails into bins has *exactly* the amount of longevity you'd expect — maybe 20 minutes before you’re done forever. I could add progression mechanics or achievements, but honestly? Fixing this would 100% turn the "dumb fun" into "yet another gamified productivity app."
### 2. Monotonous Artwork
I designed four nail types. That’s all. Small nails, big nails, bent nails, rusty nails. Any attempt at improving the aesthetics feels like polishing a turd (or a nail). There’s a reason Stardew Valley hired a full-time artist.
### 3. It’s Over-Categorized
You have bins for size, bins for shape, bins for "perceived quality." A couple of users found this all a bit much — valid complaint. One guy on the thread said, "Bro, you could get rid of half these categories and I’d still overthink it." Fair. If I ever do "Nail Sim 2.0," I'd simplify.
## Why Bother?
Honestly, part of this was just flexing the "can I ship something meaningless?" muscle. And that, I think, is valuable. Too many people (myself included) bury themselves in ideas they never finish because they aim too high. Sometimes shipping *anything*, even a dumb gimmick, is the right move.
Plus, there's real joy in watching other devs ask why this exists. Some asked if I did this ironically. My answer: kinda, but the catharsis is real.
## Final Thoughts: Should You Build Something Stupid?
Yes. Absolutely. Not everything needs to be a SaaS tool with potential TAM metrics (God, I am so sick of the term TAM). Nail sorting simulator won't pay my bills, but it reminded me why I started side projects in the first place: for fun, not for scalability.
If you're stuck on Some Big Important Idea™, try making something ridiculous next. Worst case, you’ll waste a weekend. Best case? You'll ship a thing and laugh at all the confused people who try it.
And if you want to sort virtual nails for a few minutes, you know where to find me.
