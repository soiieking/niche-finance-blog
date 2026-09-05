---
title: My Side Project Just Got a Big Update — What I Learned Launching It Again
date: '2026-09-06 02:00:05+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: Releasing v2.1 today wasn’t just a milestone; it was a reality check. Here’s
  what went right, what broke, and what I wish I knew earlier.
---

Apps or games don’t just "launch" once. We like to pretend that first push to production is The Moment™, but it’s not. My app (okay, a productivity tracker-slash-notepad) hit an inflection point today with its biggest update since launch: Version 2.1. 

It wasn’t a major overhaul—no rewritten core, no AI magic, no pivot to Web3—but it was still a huge deal for me. Here’s how the update landed, what users noticed (or didn’t), and what surprised me about re-launching something that already...exists.

## The Update: Incremental but Ambitious

The big headline for v2.1? Offline mode. Users have been asking for it since forever, and honestly, I’m embarrassed it took this long. The app is built on an Electron stack, so implementing offline functionality wasn’t trivial. I had to juggle IndexedDB (which I hate) and make sure nothing desynced because the whole point of the app is its seamlessness. 

What changed:  
- IndexedDB for offline saves. Migrated from simple localStorage, which uh... let’s just say was an MVP hack that aged like milk. 
- Slightly faster load times. Data sync used to take ~2.1 seconds per session. After optimizing calls, I got that down to ~800ms.
- Big ol’ UI polish: fonts, buttons, even the little loading spinner finally got retired. Good riddance.  

What users noticed: The offline mode. Period. The UI refresh and speed didn’t come close to the dopamine highs they got from loading their work while on a train with no Wi-Fi. Lesson? Remove pain points first; polish can wait.

## The Launch Itself: What Went Right (and What Didn’t)

Hitting "publish" on this update was nerve-wracking. Here's where I ate dirt and where I actually felt proud:

### What went wrong:  

1. **Notification fatigue exists.**  
I emailed my small user base (2,139 subs) AND posted everywhere: Twitter, r/sideproject, Discord groups. Burned a whole afternoon typing out variations of "📢 Big Update! v2.1 out now!" But guess what? Only 8% of recipients actually opened the email. Meanwhile, one post in r/SideProject got 54 upvotes and brought 143 new installs. Email marketing isn’t dead—it’s just meh unless your app's got 100K users or niche B2B features.

2. **Database bugs during rollout.**  
There’s always a bug you didn’t test for. One user (shoutout, @RubikLord on Twitter) found out offline mode didn’t work if your browser cache cleared *after* logging out. Rookie mistake. Took me two hours to fix, but by then some App Store reviews already spat out the dreaded "offline doesn’t work" complaint.

### What went right:

1. **Clear goals + versioning saved me.**  
You know what feels amazing? Typing out a laser-focused changelog. I kept this update scoped tightly to three fixes/features. As a result, QA/testing took 2 days instead of the eternal feedback loop v2.0 got stuck in. I don’t ship buggy features anymore (okay, except for RubikLord’s corner case...whatever).

2. **Small bursts of goodwill still hit hard.**  
One user, who apparently hadn’t opened the app since March, replied to my update email saying, "This update literally fixed the only thing I didn’t like about your app." Won’t lie, that one sentence kept me going through some rough hours. 

## The Psychology of Updates: Why "Big" Doesn't Always Mean "Obvious"

There’s something humbling about putting 40-ish hours into an update and realizing most people won’t clock the invisible improvements. Nobody cares that I normalized the font weights. They care that the app "feels better" but don’t know why.

If you're working on your own thing, remember this: perception of progress matters more than actual changelog length. Two minor features users begged for—and that’s it—will make them see the product differently. Over-polishing a feature nobody asked for? Total sunk cost.

## Final Stats from Launch Day

- App Store reviews: +4 within 6 hours (mostly positive)
- Installs: 143 new (thanks Reddit, again), 23 churned
- Revenue bump: Increased by $82 for now. Probably 1-2 people upgraded to Pro to try offline. We’ll see if it sustains. Not retiring early yet.

This wasn’t viral, and I’m not rich yet. But v2.1 proved something: I’m building something specific enough to make a few folks happy. That’s a hell of a motivator.

---

No FAQ here—this was a straight-up story. If you want IndexedDB setup tips or launch-day marketing ideas, shout in the comments. I read ‘em all...when I’m not debugging IndexedDB.
