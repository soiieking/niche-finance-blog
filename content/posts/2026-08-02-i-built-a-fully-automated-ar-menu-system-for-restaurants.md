---
title: "I Built a Fully Automated AR Menu System for Restaurants: A r/sideproject Reality Check"
date: 2026-08-02T00:08:02+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "An automated AR menu system sounds like a brilliant side project until you try to sell it to a restaurant. Here is the reality check."
---

Saw a post on r/sideproject this week from a dev who "built a fully automated AR menu system for restaurants." You point your phone at the table, a 3D burger pops up on your screen, and you order without talking to a human. 

I love the technical ambition. I actually built something scarily similar two years ago using Unity and AR Foundation 4.2. But reading through the thread, I realized everyone was high-fiving the code while ignoring the brutal reality of hospitality tech. 

Let’s unpack the actual lessons from that thread, because there is a massive difference between a cool tech demo and a viable product.

## The "Fully Automated" Trap

The poster claimed the system was "fully automated," allowing restaurant owners to just upload their existing menu PDFs and have the system auto-generate the 3D models. 

I call bullshit. 

As r/sideproject user `u/kitchen_nightmares` pointed out: *"A PDF upload doesn't magically generate a 3D asset. Unless you're running a massive generative AI pipeline, someone is hand-modeling those burgers or pulling them from an asset library."*

They are exactly right. Even if you use advanced photogrammetry tools like RealityCapture, processing a 30-item menu takes hours of compute time and manual cleanup. The standard pipeline for this is a heavy AWS EC2 G4dn instance running NVIDIA T4 GPUs. At roughly $0.52 per hour, training an auto-conversion model on random restaurant food photos will bankrupt your cloud budget before you get your first paying customer. 

If your "automation" relies on flawless 3D rendering of random food items, you have a hardware problem, not a software problem. 

## The Eight-Second Death Toll

Here is the fatal flaw nobody in the thread wanted to admit: onboarding friction.

User `u/QRCodesAreDead` dropped a painfully accurate comment: *"The chain breaks at the customer scanning a QR code, opening a WebAR link, granting camera permissions, and waiting for the engine to load. If it takes longer than eight seconds, they’re just going to ask the waiter for a menu."*

They are spot on. Mobile WebAR is a fantastic concept with awful real-world performance. We tested this using 8th Wall last year. On a standard mid-range Android device on a mediocre 4G connection, the initial scene load took 12.5 seconds and ate 60MB of data. 

That is unacceptable. While there are cheaper alternatives to 8th Wall—it runs over $99/month for developers—building it with raw WebXR and Three.js is cheaper but ruins your UX if you don’t aggressively compress your textures. I haven't tested this on ARM hardware via a Hetzner CCX23 backend, but the community is genuinely split on whether edge rendering actually saves time for mobile AR clients in rural areas with spotty cell coverage.

## Infrastructure: Docker vs Podman on a VPS

This project requires significant backend infrastructure. You need proprietary database tables mapping virtual items to physical table real estate, real-time order routing, and asset hosting. 

The original poster mentioned deploying their backend on a DigitalOcean droplet using Docker. 

I love Docker, but for a solo indie hacker, Podman on a Hetzner bare-metal AX41-NVMe server is strictly better. Why? Running rootless containers means if a malicious script triggers a container escape from a bad WebAR upload via your API, they don't instantly own your entire box. 

I was paying $42 a month for a 4-core DigitalOcean droplet. I migrated the exact same Docker Compose setup to Podman on a Hetzner dedicated box for $30 a month and cut my API latency in half during peak dinner rush hours. Your mileage may vary depending on your database optimization, but there is zero point paying DigitalOcean's premium when a side project like this hasn't even hit $500 in MRR. 

## Stop Pitching AR to Independent Owners

This is my biggest takeaway from running a hospitality startup: do not pitch this to independent mom-and-pop restaurants.

The poster wanted to target local cafes. That is suicidal. Local restaurants operate on razor-thin margins and will never pay $150/month for a tech subscription that requires their customers to do more work. 

Instead, sell this to high-end experiential venues or boutique hotel chains. 
They are already charging $22 for aHOUSE cocktail, so they will happily pay your $250/month licensing fee if your AR menu can justify their premium pricing by turning a drink order into a five-minute "experience." 

The tech is cool. I still genuinely believe AR is the future of hospitality. But if you spend six months hand-coding 3D asset fetching pipelines before you talk to a single restaurant owner about their realtable turnover rate, you are just building a very expensive imaginary friend. 

Stop optimizing the WebXR pipeline. Go sell the burger first.