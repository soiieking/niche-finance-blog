---
title: I Built an App That Turns Your Bad Day Into a Diss Track
date: '2026-08-28T04:00:36+08:00'
draft: false
tags:
- indie-hacker
- side projects
- technology
summary: A hilarious dive into a side project that transforms bad vibes into lyrical
  gold, powered by sharp community feedback and raw creativity.
---

### The Side Project We Didn’t Know We Needed
So, you’ve had a trash day. Your boss micromanaged you, your overpriced Bluetooth headphones died mid-call, and, oh hey, your neighbors are cranking EDM at 2AM. What if you could turn all that pent-up frustration into a diss track—custom bars dropped in your honor to tell the universe what’s up?
That’s exactly what one r/sideproject user, @codedlyrical (not their real handle, but that vibe), claims to have made: an app where users vent, and the app spits out a personalized diss track aimed squarely at your bad day. The app leans on OpenAI’s GPT-4 for lyric generation and installs basic beats so you feel like the Kendrick Lamar of mild inconveniences.
I dove into the thread, and honestly? This project’s got more going on than you’d think. But also, it’s a classic case of “hilarious hook, real UX and tech struggles.” Let’s break it down.
## How It Works (Mostly)
Here’s the flow. You open the app, input your rant—think “my landlord raised rent by 20% and laughed like an evil Marvel villain”—and the app transforms it into scathing lines of rhyme. “Your rent hike’s grotesque, like a spam-text flex,” or whatever GPT decides to cook up. There’s a play button too, so you can hear the lyrics synced over royalty-free beats that sound just passable enough for TikTok.
The developer clarified their stack in the comments: “Built in React Native, hosted on Render for now, and connected to OpenAI’s GPT-4 API (hard rate limiting though). Beats are preloaded .mp3 files, nothing dynamic yet.”
Why React Native? They said: “I wanted this on iOS and Android; React Native is overkill *for most projects* but for this? Cross-platform was key.” Fair point.
That’s the magic trick. But the thread also exposed some glaring pain points...
### The Problem With Diss Track Automation
For starters, GPT-4 sometimes misses the mark. @caffeinel00p commented, “I tried it with ‘I bombed my presentation,’ and it generated bars suggesting *I made the presentation bomb on purpose.* Like, I’m not trying to be a Bond villain.”
This tracks with GPT’s tendency to preference puns over emotional nuance. Yeah, it’s funny when your frustration turns into “You’re late to the date, like a cached Chrome tab,” but users want catharsis, not sarcastic AI misfires. @codedlyrical admitted, “Fine-tuning is on the roadmap, but I need revenue first—OpenAI costs are killing me.”
And then there’s the delivery problem. Currently, you can’t upload your own voice (or Morgan Freeman’s) to sync the lyrics; everything’s read out in that AI-robot monotone. Several users flagged this. “Let me rap it myself, or at least make it more fun to share,” said @lofiRamen. They’re not wrong.
## Why People Love It Anyway
Even with these kinks, the app taps into something universally funny and satisfying. We’ve all experienced the “let me meme my sadness” instinct. This tool just weaponizes it in a way that feels new—and a little chaotic. One user called it “DissTrackGPT,” which honestly would’ve been an amazing name.
@battlecode86: “This is the best dumb app since that guy made Yo.” Not sure if that’s praise or an insult, but either way… it’s an achievement. If nothing else, people like side projects that just *exist to exist.* Not every app needs to disrupt an industry, and sometimes, just making someone laugh for a second is enough.
### What’s Missing (and What’s Next)
The two biggest feature requests feel obvious:
1. **Better customization.** Let users tweak lyrics, pick punchlines, or add their vocals for true ownership. “This wouldn’t even be hard... Stitch in ElevenLabs or Uberduck for voiceovers,” suggested @devpirate404.
2. **Revenue plans.** The dev admits, “There’s a paid tier planned (custom beats, better AI), but no ads ever.” A good choice, but sustainability’s key. Spitting rhymes won’t pay server bills forever.
In the wild r/sideproject spirit though, people are throwing out memeable ideas: integration with Spotify playlists, battle modes, or even sending anonymous diss tracks to your enemies. High-effort trolling? Maybe. Entertaining? Absolutely.
### TL;DR
This app won’t get you a Grammy, but it might save your sanity after a long day. It’s a scrappy, chaotic mix of venting digital angst and laughing at yourself—and there’s something oddly pure about that.
Sure, it’s got rough spots. The AI misses context, the beats could slap harder, and the long-term utility is a question mark. But hey, it brought me (and Reddit) joy today. Go play with it, roast your bad coffee, and enjoy the dopamine hit of calling life out.
