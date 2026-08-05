---
title: "I Let a Virtual Pet Live in My Browser's Extension APIs"
date: 2026-08-05T12:00:28+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "Someone on r/sideproject built a desktop pet that lives in your DOM and punishes you for reading Hacker News. I built it, broke it, and ran the numbers."
---

I was deep in a three-hour Reddit spiral on r/sideproject when I found a post with a title that finally got me to look up from my terminal: "What if your browser had a tiny creature living inside it?" 

It wasn't another SaaS wrapper or a crypto scam. The premise was simple. An extension adds a tiny pixel-art creature that lives in the corner of your browser viewport. It feeds on your actual browsing behavior. Read Hacker News all morning? It gets lethargic and sleeps. Knock out a 2,000-word blog post or actively push code to your repo? It gets hyped. Basically a Tamagotchi for internet productivity.

I immediately forked the repo. Because if you spend enough time on side project forums, you know the only thing indie hackers love more than building a Jira clone is building a tool to aggressively shame you for your own discipline.

## The architecture weirdly makes sense

The original poster was pretty much driving blind on the exact tech stack, but the approach was slick. They’re using a standard MV3 service worker using Chrome's `history` and `tabs` APIs to pull the domain strings every 30 seconds, feeding that to a local SQLite WASM blob to calculate state, and rendering the sprite via a pure CSS keyframe animation.

I love this stack. It runs entirely on the client. You don't need to pay $20/month for DigitalOcean to host a microservice just to manage a 16x16 pixel duck. 

But there's one fatal flaw. At idle, persisting this creature's raw state to IndexedDB was eating a wild 35MB of RAM on my 2021 M1 MacBook. For a tiny pixel duck, that's absurd. I spent forty minutes ripping out the bloated IndexedDB sync and replacing it with a simple debounce that just pushes a JSON string to `chrome.storage.local`. RAM usage dropped to 4MB. 

Your browser is already an absolute hog—my Chrome is currently eating 6GB with 14 tabs open. You don't need a pet requiring its own dedicated 35MB allocation just to remember it ate a virtual seed.

### The distraction economy meets your real DOM

Here's where the side project community is genuinely split. Does this tool actually curb procrastination, or is it just another distraction?

One commenter in the thread said they wanted to add integration with RescueTime to track active window focus properly instead of relying purely on the URL. I think that's massive overkill for most people. The beauty of a side project is the constraint. Tracking the URL hostname is about 90% accurate out of the box. If you're on Twitter, you're not working. If you're re-reading the TailwindCSS v3.4 docs for the fifth time this week, you probably are. 

Actually, running on pure URL tracking has a brilliant side effect. If you're a developer who literally has `github.com` open all day, your browser pet will think you're an absolute productivity god. But the minute you tab over to YouTube to watch a 45-minute LTT video on a mouse review, it flags you as distracted. 

## Measuring the psychological impact

I forced my friend to test this with me for a week. Her creature evolved into its final "rage" form on day two because she kept falling down Wikipedia rabbit holes. Mine maxed out its health bar because I was crunching out a Hugo theme. 

The weirdest part of the experiment was the immediate dopamine feedback loop. It genuinely feels good when the stupid little pixel blob does a backflip because you finally hit "Save" on that blog post you'd been agonizing over for three weeks. 

Look, is this going to replace RescueTime? Absolutely not. Is it a massive drain on your afternoon to build one yourself from scratch? Also no. The core logic took me about two hours and $0 in server costs to adapt. It’s a fun weekend hack. And honestly, I care way more about keeping a virtual dinosaur alive than any of the 14 different standardized, server-hosted habit trackers I abandoned by week two.