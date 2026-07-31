---
title: "I'm So Over AI SideProjects: Here's What Actually Works in 2026"
date: 2026-08-01T01:49:57+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "Sick of every r/sideproject post being another AI wrapper? Here are real, dumb, useful projects from the trenches."
---

Spent way too much time on r/sideproject this week. I don't know about you, but if I see one more "I built an AI wrapper that summarizes PDFs" post, I'm going to throw my mechanical keyboard out the window. 

The tide is finally turning. A few days ago, a thread popped up titled "Share your Not-AI projects," and the response was massive. People are tired of prompt engineering. 

We want to build real, dumb, useful tools. Here are a few from that thread I actually tested.

## The Painfully Boring Uptime Monitor
A user named `u/hetzner_or_bust` posted about a status page they wrote in Go and dumped on a $4 Hetzner CX22. It pings an endpoint every 30 seconds. That's it. No vector databases, no fancy embeddings. Just a basic binary that writes to SQLite and sends a webhook to Discord if HTTP 200 doesn't come back within 2000ms.

I forked the repo last night and deployed it myself. Setup took 11 minutes. RAM usage sits at a flat 14MB. 

For $29 a month, Pingdom does essentially the same thing. For $4 a month, you get full control. My only gripe is that he bundled the frontend directly into the Go binary instead of serving it as a separate static file build. It makes modifying the UI a massive pain in the ass. Still, it's a refreshing change of pace from people trying to charge $20 a month for a thin UI over the OpenAI API.

### Why do we forget the simple stuff?
We all get collectively blinded by shiny new tech trends. I've built SaaS apps that needed 2GB of RAM just to run a background worker queue. Looking at this Go binary, which uses fewer system resources than my terminal text editor, makes me realize how much architectural overkill we tolerate on a daily basis. It's pure insanity.

## A Markdown-to-PDF Invoice Generator
Another redditor posted `pdfscript`. It takes Markdown, parses basic variables using Go templates, and spits out a printable PDF using a headless Chrome runner. 

I used to lean on Stripe for invoicing. Stripe is great until you realize you're paying a 2.9% premium just for a button that says "Pay Now." Lately, I've just been sending manual wire transfers. 

I tested this tool on a M2 MacBook Air and a standard Ubuntu 24.04 VM. Locally, it generates a two-page invoice in 400ms. On the Linux box, it ran nearly the exact same speed. The catch? The headless Chrome dependency bloats the Docker image to a chunky 1.2GB. If you deploy this, do it bare-metal or use Podman with a slim ubuntu base to save disk space. It's overkill to run a full browser instance just to print text. But honestly? Your mileage may vary, and it's still better than paying a monthly subscription for basic PDF generation.

## The Retro Pixel Art CDN
This is the project that actually made me jealous. 

A user built a free image cache and CDN specifically tailored for seamless pixel-art tiles. Web game devs constantly steal sprites from OpenGameArt.org, but hotlinking them destroys your page rank and eats your bandwidth. This proxy caches the PNGs and serves them with the exact correct `image-rendering: pixelated` headers.

I haven't tested this on ARM architecture yet, so I can't speak to how it runs on a Raspberry Pi. The community was genuinely split on whether nginx or a custom Node server was the better backend for this specific use case. Honestly, for static asset caching, just use nginx. Always use nginx. I don't care how good your JavaScript is.

## Stop trying to build neural networks. Build a screwdriver.
If you want to actually finish a project this weekend, stop trying to train a custom GPT model to recommend Diablo IV builds. Build something offline. Build something that solves a deeply stupid, highly specific problem. 

My current side project is a Python script that moves files older than 60 days from my Downloads folder to an archive drive. Zero AI involved. It took an hour to write. I use it every day. 

Go build your own screwdriver.