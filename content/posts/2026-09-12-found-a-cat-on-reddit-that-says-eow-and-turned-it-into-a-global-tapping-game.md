---
title: How I Turned a Cat That Says 'Eow' Into a Tapping Game That Actually Took Off
date: '2026-09-12 12:00:03+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: How a weird cat meme from Reddit became the dumbest, most addictive global
  tapping game—and a surprising monetization experiment.
---

## The Dumbest Idea I’ve Ever Had (That Worked)

Okay, full disclosure: this started as a joke. I was doom-scrolling **r/sideproject**, procrastinating on an actually important deadline, when I found this video. A cat says "eow" instead of "meow." That's it. That’s the joke. 

But here’s the thing—you know how once you hear a word said weirdly, you can’t unhear it? That cat gave me brainworms. "Eow" became the sound of everything in my head. Tap a button? Eow. Get a Slack ping? Eow. Someone replies "haha" to your extremely good Tweet? Big eow. 

So naturally, the only logical next step was to make a *game* out of it.

## From "Let’s Ship It for the LOLs" to Going Global

Making an ultra-simple tapping game doesn’t require much. I hacked together the first version in **Godot (v3.5.2)** because it’s light, fast, and free. Think Cookie Clicker meets Flappy Bird, but instead of flapping or cookies, you get rewarded with progressively louder "EOWs" every time you tap.

One Saturday afternoon, I recorded the sound (yes, with that crunchy $30 USB mic people use for Zoom), slapped in a free sprite of the cat (thank you, OpenGameArt), and added leaderboards tied to Firebase. The whole thing took five hours because the game loop is brain-dead simple: Tap cat. Cat says "eow." Score goes up.

I posted the link back to the subreddit: "Found the ‘eow’ cat, made a tapping game. Try it if you’re bored."

150 downloads in an hour. Cool. By the next morning, it was 4,000. By day three, **17,000 people across 12 countries had decided this was how they’d waste time.** 

I couldn’t believe it.

## Lessons in Scalability (a.k.a. My Firebase Meltdown)

So, here’s the part people don’t tell you when you accidentally go viral: your backend *will* crumple like a wet napkin. Mine sure did.

I chose the Firebase free tier because "this is just a silly meme app, what’s the worst that could happen?" Answer: **Firebase caps simultaneous connections at 100.** At the 101st "eow," the leaderboards were toast. I panicked, Googled "Firebase alternatives," then panicked more when I realized basically none of them had free plans I could switch to immediately.

I threw money at the problem—$25/month to upgrade my Firebase to a tier that wouldn’t block more players. But if I hadn't caught the surge fast enough, it would’ve tanked the app’s early momentum. Lesson learned: *always prepare for success, even if your project feels dumb and niche.*

## Monetizing Stupid: A Surprisingly Difficult Balance

With so many people playing "Eow Tapper" (yes, I gave it a name), I reluctantly thought about monetization. To be clear, I hate ads, but running servers isn’t free. Here’s what I tested:

1. **Interstitial ads (Unity Ads)**: CTR hovered around 2.2%, and my earnings were a whopping $0.32 per user. Yikes.
2. **"Pay to Customize Your Cat" DLC**: $2.99 to unlock hats for the cat. This murdered the conversion rate (0.6%), but the comments were gold: "Bro let me see the eow cat in a cowboy hat, take my money."
3. **Donation via Buy Me a Coffee**: Surprisingly effective. About 4-5% of players kicked in $5. 

The takeaway? For dumb, fun meme projects like this, people will pay if you keep it optional and ridiculous. Don’t nickel-and-dime them.

## What Worked, What Broke

**What worked:** Leaning into the absurdity. The game got reposted to Twitter (excuse me, *X*) with captions like "Why am I obsessed with this???" The virality grew because people wanted to share something idiotic and fun.

**What broke:** Leaderboards. Turns out, people *cheat*. Someone reverse-engineered the score encryption (lol, I barely tried to protect it) and uploaded a leaderboard score of 999,999 taps. I had to wipe the boards twice and eventually disabled real-time updates entirely.

## Final Numbers

The whole thing petered out after two months (memes have shelf lives), but here’s how it netted out:

- **Downloads**: ~118,000 users total.
- **Revenue**: About $2,850, mainly from donations.
- **Time spent**: ~20 hours across the whole lifecycle.
- **Lessons learned**: Priceless? (Sorry, I know that’s corny, but seriously.)

Would I do this again? Absolutely. Am I also retiring from tapping games forever? Hard yes.

---

### FAQ

#### Why didn’t you use Unity instead of Godot?
Unity’s revenue-sharing model felt weird for something this small—plus, Godot is just easier to spin up when you need a no-frills 2D app. For something heftier, I’d probably reconsider.

#### Can you still download the game?
Nope. I took it down when Firebase costs climbed over $30/month during the last spike. Not paying recurring fees for a dead meme, sorry.

#### How’d you handle audio latency on mobile?
I...didn’t. iOS is fine by default, Android sometimes delays a few frames. Nobody cared because it’s "Eow."
