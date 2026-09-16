---
title: 'subsmap: How a Side Project Uncovered Reddit''s NSFW Problem (or Feature?)'
date: '2026-09-17 00:00:04+08:00'
draft: false
tags:
- indie-hacker
- analytics
- reddit
summary: 'subsmap: a Reddit analytics tool accidentally discovers 1/3rd of Reddit''s
  active communities are NSFW. Intentional or just chaos?'
---

## The Fun of Building Something You Didn’t Fully Understand

Earlier this week, someone in r/sideproject posted about **subsmap**, a Reddit analytics tool they’d hacked together. The pitch was simple: map out subreddit activity—figure out which ones are growing, which ones are dying, and, if you're lucky, spot trends early. Classic data nerd side project, right? 

Turns out, the thing works a little *too* well. While chewing through the top 50,000 subreddits, **subsmap** stumbled across a weird fact: about one-third of Reddit’s active subs are labeled NSFW. Yep, about 16K out of 50K are the digital equivalent of that drawer in your house you don’t open when guests come over. 

Was this a bug? A quirk in how Reddit labels things? Nope. Just Reddit being Reddit.

## The Core Idea Behind subsmap 

subsmap scrapes subreddit metadata via the Reddit API. It grabs things like the number of subscribers, growth over time, post frequency, and—here’s the kicker—whether a subreddit is flagged NSFW. It works out of the box with Python and a couple library dependencies (Praw for the API, Matplotlib for charting). I cloned the GitHub repo, ran `pip install -r requirements.txt`, and had it pulling stats within 10 minutes. 

The UI? It doesn’t have one. It’s an “export to CSV and parse it yourself” kind of deal. Which is fine—for now. Honestly, I’d rather a minimum-viable CLI tool than some over-engineered React app running on Heroku at 2 FPS. 

But the *fun* kicked in when the guy who built it shared his test results. Buried in the CSVs was the accidental discovery of Reddit's NSFW ratio. Let’s be clear: this wasn’t some moral grandstanding. He just noticed a pattern while debugging and thought it was funny enough to share. 

## How NSFW Took Over Reddit

Here are the numbers from his dataset that stuck with me:

- Out of ~50,000 active subs (meaning they get posts at least weekly), 16,345 had the NSFW flag.
- The growth rate of NSFW subs outpaces non-NSFW subs by about 20% on average. 
- Many “borderline” NSFW subs (like r/oddlysexy) are clearly exploiting it as a traffic hack. Label your sub NSFW → dodge bans → win eyeballs. 

Some of the r/sideproject comments were predictably cynical: “This has to be cooking Reddit’s engagement stats.” One user (u/data-bananas or something equally Reddity) suggested that NSFW is a double-edged sword for platform health. Sure, the communities are active, but they bring moderation headaches—and we all saw how that played out with Twitter-turned-X. I still have nightmares about Musk doubling down on "free speech zones" and basically burning the house down.

## Does This Change Anything for Indie Hackers?

If you’re building tools for Reddit (or scraping Reddit for some AI/data play), this should be a flashing warning sign. The platform’s traffic pie has some spicy flavors, and if you don’t want your analytics tool to look like softcore clickbait in a pitch deck, you'll need filters. 

Tools like subsmap—no matter how barebones—are revealing quirks about Reddit I didn’t fully appreciate before. You’d think subs like r/askscience or r/productivity are driving Reddit’s activity stats. Nope. It’s subs like r/thickthighs. And if you want to avoid NSFW data tainting your analysis, you'd better plan for it upfront. 

For example: Building ML models to classify subreddit quality? Train on clean data. Doing API pulls for feature discovery? Probably blacklist NSFW tags unless that’s your niche. You may want to harden your tool to dodge Reddit’s rate limits and evolving API drama (RIP third-party apps in mid-2023).

### What I Liked About subsmap 

1. **Super simple setup:** I didn’t get stuck anywhere. Clone → pip → run. No Docker. No mystery dependencies that break on Mac M1s. Just Python, the way it should be. And no wacky billing tiers—you need a Reddit API key, but that’s just standard.
   
2. **Brutally honest results:** The NSFW ratio wasn’t some “hidden feature” you had to hunt for; it fell right out of the data. Sometimes, simplicity shines.

### What Needs Work 

1. **No visualizations baked in:** The CSV export is fine if you live for Excel, but I build everything in Notion and didn’t feel like writing extra code for something this niche. Even a couple pre-made exports—bar charts, heatmaps—would go a long way toward making this tool feel polished. 

2. **Doesn't handle large datasets gracefully:** On my 16GB RAM setup, the script slowed to a crawl parsing the largest subs. This probably comes down to inefficient API calls or data joins. With some PySpark or async tweaking, you could scale it better. 

## What’s Next for This Weird Corner of Reddit Analytics?

Honestly? Subreddit analytics are a rabbit hole because Reddit doesn’t play nice forever. Today’s API loophole is tomorrow’s overpriced premium plan. If I were the author of subsmap, I’d put 20 hours into turning this into a dashboard (maybe Supabase + Next.js to keep it frugal) and then toss it on Product Hunt. People love tools like this, even half-baked ones.

But the real goldmine here? **Understanding Reddit’s traffic tricks.** Whether you’re selling SaaS, running ad campaigns, or just procrastinating at work, knowing what grows (and why) gives you a stupidly unfair advantage. 

NSFW might be 33% of the equation. But the rest? That’s your job to figure out.

---
