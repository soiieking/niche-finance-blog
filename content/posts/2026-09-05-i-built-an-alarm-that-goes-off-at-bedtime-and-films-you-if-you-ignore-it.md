---
title: I Built a Bedtime Alarm That Films You Ignoring It — Here's Why That Matters
date: '2026-09-05 20:00:04+08:00'
draft: false
tags:
- indie-hacker
- hardware
- behavior
summary: An alarm clock that shames you into sleeping on time? Overkill? Maybe. But
  this project might tap into something deeper about self-control tech.
---

## The Alarm Clock That Stares Back at You

Someone on r/sideproject built an alarm that not only reminds you to go to bed — it *films you* if you don’t. Yeah, you read that right: ignore the bedtime alert, and this thing records your rebellion for posterity (or blackmail material).

The creator claims this was half-joke, half-real attempt to fix their own sleep habits. And frankly? Kinda brilliant. Creepy, yes, but brilliant. Because this isn’t just another “smart” alarm clock. It’s gamifying mortification. If the idea of watching yourself zombie-scroll TikTok for 3 hours at 2 a.m. doesn’t shame you into better behavior, I don’t know what will.

But does it actually matter? Or is it just a novelty destined for 15 upvotes and a forgotten GitHub repo?

## Why Bedtime Alarms Aren’t Mainstream (Yet)

Most tech meant to improve your life does one of two things:  
1. Guilt-trips you (think: your Fitbit telling you you’ve been sedentary since lunch).  
2. Flat-out annoys you (smart fridges sending you pings for being open too long).  

Yet, sleep-tracking hardware/software is surprisingly bad at both. Sure, your Apple Watch can log your REM cycles and say “You need 7 hours of sleep!” — but that’s *reactive*. By the time it tells you how bad your sleep is, the damage is already done.  

There’s a small-but-persistent gap in tech for *enforcing* behavior changes, especially for sleep. Filming someone ignoring an alarm breaches that boring, passive line. It’s borderline dystopian, sure, but it does something most apps don’t: makes your bad habits impossible to unsee.  

In one thread comment, someone suggested this could be “better served by an accountability partner, not surveillance.” Which, valid. But accountability partners aren’t scalable, while a bespoke shame-machine is. At least in theory.  

## It’s 2026 — Don’t We Have Better Tools Already?

This alarm hits a nerve because, honestly, self-control tech hasn’t evolved much in years. Here’s the state of things:  
- **Apps like Sleep Cycle** ($40.99/year) analyze sounds and wake you gently based on movement. Cute, but can’t pull you off Instagram at 1 a.m.  
- **IoT gadgets like Loftie** (a $150 clock) promise less connectedness at night — but it’s strictly carrot, no stick.  
- **Software blockers like Freedom and StayFocusd** work for doomscrolling during the day, but they miss the tactile, in-your-face experience of hardware.  

This side project kind of threads the needle. It combines psychological manipulation (shame), hardware-level friction (you can’t just snooze a camera easily), and a little novelty for good measure.  

Does everyone need this level of adversarial tech to sleep better? No. But for hardcore doom-delay procrastinators? Possibly life-changing.  

## What Could Actually Make It Useful?  

The current prototype is absurdly simple: a Raspberry Pi with a webcam, a speaker for the alarm, and some hacked-together Python to handle the video recording. Step up the execution, though, and this thing could become weirdly mainstream.  

Here’s what might take it from side project to Kickstarter darling:  
- **Cloud integration.** Automatically uploading those videos (encrypted) for review later would be next-level accountability.  
- **AI analysis.** Recognize when you’re scrolling versus, say, meditating or reading. The tech is there; ChatGPT APIs and object recognition can get you very close.  
- **UX upgrades.** Right now, it’s fine for tinkerers. But sell it as a polished $99 device with an app, and you solve the out-of-the-box friction for normies.  

That said, no amount of tech will make this more than a niche product. Lots of people will balk at being filmed at their most vulnerable (bedtime procrastination is basically self-sabotage in slow motion). Others won’t care enough about improving their sleep habits.  

And let’s be real: for many people, just setting a hard cutoff on their phone apps would have the same effect *without* Orwellian vibes.  

## The Bigger Picture: “Adversarial” Technology

This matters because it’s part of a growing trend: people building devices to counteract their own worst impulses. We’ve seen this in light ways before (like the Kitchen Safe that locks up your phone or snacks for hours). But the deliberate act of *documenting failure* is rare and more personal.  

It makes you reframe the tech. This isn’t just about sleep. It’s about creating systems where screwing up has real, tangible feedback. Welp, you stayed up — here’s proof you can’t ignore.  

Would I personally install this? Nah. Too lazy to DIY, too paranoid about camera hacks. But I can see the appeal.  

Someone in the thread joked, “This is tech literally fighting you for your future self.” Sounds about right.  

---

### FAQ

#### **Why not just use app timers or phone locks at bedtime?**  
App timers (like iOS Screen Time or Focus mode) are great for passive control. But they’re easy to override with a few taps, which makes it harder to stick to your goal. A hardware device like this adds friction and ups the stakes.

#### **Isn’t this a bit invasive to your privacy?**  
Absolutely. Recording yourself could turn a lot of people off. But for hardcore procrastinators, the trade-off between mild embarrassment and better sleep could make it worth it. (Plus, no one's forcing you to upload these videos anywhere.)

#### **Could this be useful for habits beyond sleep?**  
Totally. The concept could easily extend to other bad habits — like procrastinating on work, skipping workouts, or overeating. It’d be a weird niche, but the shame-based approach has universal potential.  

<script type="application/ld+json">  
{  
  "@context": "https://schema.org",  
  "@type": "FAQPage",  
  "mainEntity": [  
    {  
      "@type": "Question",  
      "name": "Why not just use app timers or phone locks at bedtime?",  
      "acceptedAnswer": {  
        "@type": "Answer",  
        "text": "App timers (like iOS Screen Time or Focus mode) are great for passive control. But they’re easy to override with a few taps, which makes it harder to stick to your goal. A hardware device like this adds friction and ups the stakes."  
      }  
    },  
    {  
      "@type": "Question",  
      "name": "Isn’t this a bit invasive to your privacy?",  
      "acceptedAnswer": {  
        "@type": "Answer",  
        "text": "Absolutely. Recording yourself could turn a lot of people off. But for hardcore procrastinators, the trade-off between mild embarrassment and better sleep could make it worth it. (Plus, no one's forcing you to upload these videos anywhere.)"  
      }  
    },  
    {  
      "@type": "Question",  
      "name": "Could this be useful for habits beyond sleep?",  
      "acceptedAnswer": {  
        "@type": "Answer",  
        "text": "Totally. The concept could easily extend to other bad habits — like procrastinating on work, skipping workouts, or overeating. It’d be a weird niche, but the shame-based approach has universal potential."  
      }  
    }  
  ]  
}  
</script>
