---
title: "Karakeep 0.33 is Here: Semantic Search Finally Doesn't Require a Server Farm"
date: 2026-08-03T14:41:09+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Karakeep 0.33 drops with local semantic search and offline reading. Is the Ollama RAM overhead worth it? Yes, and here is why."
---

I’ve been running Karakeep since back when it was just a clunky Cloudflare-scrapping bookmark manager. Version 0.33 just dropped, and it actually fixes the two biggest reasons I kept alt-tabbing back to Omnivore or Pocket. We finally get semantic search and offline reading. 

This isn't just a minor patch; it fundamentally changes how you interact with your saved links.

## Semantic Search: Actually Finding That One Article

Keyword search is dead. Long live semantic search. 

Instead of desperately trying to remember if you tagged a bookmark "homelab," "proxmox," or "vps," Karakeep now uses local embeddings to actually understand what the article is about. You search "cheap ARM server," and it pulls up that Hetzner vs DigitalOcean benchmark you saved six months ago without requiring exact string matches. 

But here’s the catch: doing this right means running an embedding model, usually through Ollama. One user in the release thread was complaining about their API bills from OpenAI, but running it locally is the whole point of self-hosting. That said, the RAM overhead is real, and your mileage may vary.

If you’re already running Ollama on a beefy desktop, this is a zero-effort win. If you’re trying to cram Karakeep onto a $4/month 1GB Hetzner CX22, forget about local embeddings. The `nomic-embed-text` model is surprisingly lightweight, but it still wants at least 1.5GB of RAM to itself without panicking the kernel OOM killer. The community is genuinely split on whether to use the built-in Ollama integration or just stick to the standard SQLite full-text search. My take? If you have the spare 2GB of RAM, run the embeddings. It changes the utility of the app entirely. 

## Offline Reading Finally Works Without Hacking Docker

Reading a saved article on a train without Wi-Fi used to be a massive pain. You usually had to rely on broken reader-view parsers or manually download the HTML. 0.33 handles this properly.

It now aggressively extracts and caches the actual text and images, serving a stripped-down, offline-ready version automatically. I grabbed it via Docker Compose, and the setup was genuinely 30 seconds of work. 

It just works. The real test for me was loading up an article on a flight this week. I expected a "No Connection" error like I got on my old Pocket setup. Instead, Karakeep handed me the full readable text with cached images. It’s exactly what self-hosters want: zero friction, zero cloud reliance, and it works when the internet goes down.

### The Elephant in the Deployment

I love this tool, but it has one minor flaw that trips up newcomers. To get the offline fetching to work reliably on Reddit posts and news sites with aggressive bot-blocking, you basically need to route the fetcher through a residential proxy or risk getting served Cloudflare CAPTCHAs. 

I haven't tested this specifically on ARM yet—my Orange Pi 5 is currently running a Pi-hole cluster, not a bookmark manager—but on x86, the scraping success rate sits at roughly 80% without proxies. 

Some folks in the thread argued this is overkill for most people, pointing out that standard bookmarking doesn't need a 500MB container alongside a separate database just to save URLs. They aren't wrong. If you just want to save links, use a basic Linkding instance. 
But if you actually want to build a searchable archive of readable content that doesn't phone home to Google or Mozilla, Karakeep 0.33 is finally the complete package. Just make sure your VPS has enough RAM to handle the AI tax.