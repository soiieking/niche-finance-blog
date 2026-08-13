---
title: "I Turned My 80s/90s Obsession Into a Side Hustle That Actually Pays Rent"
date: 2026-08-13T08:00:20+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "One r/sideproject user turned retro tech nostalgia into a real business. Here's how it works, what breaks, and what's overkill."
---

Some guy on r/sideproject posted three weeks ago: "I turned my obsession with the 80s and 90s into a business." The thread got 1,400 upvotes and, predictably, half the comments were people asking "how do I do this too?" The other half were people shitting on the idea. Both groups were right.

This isn't a story about flipping vintage Game Boys on eBay. That market's been dead since 2019 — too many YouTubers with twin peaks haircuts buying up stock. It's about something narrower: selling **retro-themed digital tools and templates** to the generation that never actually lived through the 80s but romanticizes it anyway.

## The Business Model, Explained Bluntly

The OP wasn't selling nostalgia. He was selling **curated utility**. His flagship product is a Notion template pack that mimics the aesthetic of old terminal UIs — green phosphor text on black, dashed borders, "DOS-style" command logs for daily planning. It's $19. He sold 300 copies in the first month.

That's $5,700 gross on a one-time build. Not bad for something that took him two weekends.

The key insight from the thread: **nobody pays for a wallpaper, but they'll pay for a system that looks like one.** The template does real work — task tracking, habit logging — it just wears an aesthetic that makes middle-aged Millennials feel something. That's the entire trick.

## So You Want To Copy Him? Three Paths

The comment section had three broad strategies. I poked at all of them, and here's the honest breakdown.

### Path One: The Notion Template Grind (Low Barrier, Low Ceiling)

Notion is where he started. The API makes it easy to package and distribute goods, but you're held hostage by platform changes. When Notion swapped their iOS app's rendering engine in 2023, half his screenshots looked outdated overnight. He didn't move fast to fix them, and sales dipped for a week. That's the risk.

**Cost to start:** $0.
**Time to first sale:** Under 30 days if you copy his exact route.
**The fatal flaw:** Notion users are cheap. $19 was the ceiling; at $29 he saw churn. You're fighting the platform's own free tier every step.

### Path Two: The Self-Hosted / Docker Route (Harder, But You Own It)

One commenter suggested skipping Notion entirely and building a self-hosted dashboard that generates the retro terminal aesthetic locally. Think a homepage dashboard with old CRT scanline effects — you know, the kind of thing served up via Docker Compose on a $6 Hetzner CX22 instance.

I love this approach because it actually works and you own it. **The gotcha:** the maintenance curve is steep. Docker updates break your containers; Podman is a genuinely better choice if you're on openSUSE or Fedora, but the tutorial ecosystem is thinner. The commenter admitted he spends two hours a week just keeping the thing alive.

That's not a business at $19 a pop. That's a hobby with billing enabled.

Those DIY CRTs have to go.

### Path Three: The Content + Affiliate Stack (The Boring Winner)

The highest-engaged comment wasn't a product at all. It was a guy who runs a revival of 90s-style personal webrings. He curates ~100 indie sites and sells a directory listing ($5/mo) plus affiliate links for retro hardware websites and premium themes. He makes $900/month on a $10 DigitalOcean droplet, and he can point to his own blog as an income overlay.

This is overkill for most people. I'd rather do that than reload a Docker registry every Friday. The trade-off is you're building a media business, not a product one — which means your fate is tied to SEO changes instead of API updates. Pick your poison.

## What the Thread Got Wrong

A lot of commenters obsessed over scaling. "How do you get to $10k/month?" But that's the wrong question for a side project. The highest-leverage move is automating the delivery pipeline — payments, license keys, PDF generation — and then stepping back to let it drip.

I also noticed two commenters genuinely arguing about whether using AI-generated pixel art is "cheating." That's a real split in the community, and there's no right answer. Your mileage may vary, but I'd lean toward AI for assets because buyers care more about consistency than provenance. Nobody's art-directing a Notion template.

One caveat: I haven't tested this on ARM, so if you're running an Apple Silicon machine and planning the Docker path, don't trust my benchmarks blindly. The community is genuinely split on whether ARM's fine or if wait, just use a $25 Hetzner box and skip the headache entirely.

## The Honest Verdict

If you're chasing the nostalgia angle too, rim your expectations. The 80s/90s caller list is crowded, but the paying segment is still small. The guy made $5,700 first month, but he hasn't hit that number since. It's now averaging $1,200/month passive, which is solid but not life-changing.

The real lesson: **aesthetic nostalgia is a Trojan horse for utility.** Nobody remembers the exact green tint of an IBM 5153 CRT. They just want their to-do list to feel less like spreadsheets and more like a hacker movie. That's the only trick that matters.

## FAQ

**Do I need coding skills to start with Notion templates?**
No. Notion's builder is entirely visual, and the API is optional for selling via third-party marketplaces like Gumroad. The OP knew zero JavaScript. You'll spend more time on copywriting than on logic.

**Is Docker the only way to self-host retro tools?**
No. Docker works but Podman is daemonless and better on ARM. If you don't care about containerization at all, a plain static site on Cloudflare Pages handles CRTs and audio effects with zero server cost. Setup time: about 4 hours vs. a full weekend for Docker.

**How do you price a nostalgic digital product without underpricing yourself?**
Start at $15, raise to $25 after your first 50 purchases, and never compete on price with Notion's free tier. Your deliverable is aesthetic + workflow, and people pay for time saved troubleshooting both.