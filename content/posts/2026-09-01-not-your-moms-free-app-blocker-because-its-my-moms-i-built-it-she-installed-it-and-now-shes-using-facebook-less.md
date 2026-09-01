---
title: 'Not Your Mom''s Free App Blocker (Because It''s My Mom''s: I Built It, She
  Installed It, and Now She''s Using Facebook Less!)'
date: '2026-09-01T10:00:14+08:00'
draft: false
tags:
- indie-hacker
- tools
- productivity
summary: How I accidentally turned my mom into the first beta tester for my app blocker.
---

So, my mom was at my house the other day, scrolling on her phone like a zombie (her words, not mine). She pulls up a cat video on Facebook and goes, "I waste so much time on this. Can you fix it?" And that's how this started. I didn’t set out to build an app blocker, but here we are—and my mom is actually using it.  
Spoiler: This isn’t some fancy $5/month SaaS with Machine Learning™. It’s a scrappy, free app, and it’s been shockingly effective. Keep reading if you’re tired of those overcomplicated blockers that throw “gamification” buzzwords at the wall.
## Why Build My Own?
I tried the usual suspects first: Freedom, FocusMe, even StayFocusd. They’re all decent for most people, but my mom hated them.  
- **Freedom:** "What's this subscription nonsense? I just want to block Facebook, not pay rent for it."
- **FocusMe:** Great, but the UI gave her flashbacks to Windows XP. And it costs $7/month.
- **StayFocusd:** Chrome-only. That's a hard pass for someone whose entire tech support is me.
Also, I wanted something simple—no analytics dashboards or motivational quotes. Just blocks apps. Period.
## The Tech Stack (or Lack Thereof)
This isn’t some app built in Go with 12 Docker containers. It’s *dumb* simple on purpose.  
- **Platform:** Android only (sorry, iPhone folks—Apple hates sideloading).  
- **Main tools:** Kotlin with the Device Policy Manager API. If you’ve never messed with this API, it's an easy way to block apps natively without needing a VPN or a background service.  
- **Size:** 2.3 MB APK, all-in. Works on Android 9 and up.  
The codebase is embarrassingly small. I literally used Jetpack Compose for a "select your evil app" UI that looks like an early 2010s to-do list app. But hey, it works. Setup time? Two minutes tops, assuming you can explain “Enable Admin Permission” over the phone.
## Does It Actually Work for Her?
Shockingly, yes. My mom (who is very much *not* techy) has been using it for three weeks, and her Facebook screen time is down **54%** according to Digital Wellbeing stats. She’s not perfect—she still unblocks Facebook after dinner some nights—but the friction alone is enough to make her more mindful.  
Her feedback:  
- "It’s like having a bouncer for my phone."  
- "The block button should sound angrier." (This... may end up in v1.1.)
## Why I’m Not Releasing It (Yet)
Here’s the thing: while it works fine for her, this is overkill for most people. I’ve only tested it on a handful of devices, all running stock Android. No clue if it works on Xiaomi phones with MIUI or those random $99 Samsung Galaxy A0.1 Ultra-lite-SE knockoffs sold on AliExpress.  
Plus, I haven’t touched issues like scheduled blocks, notifications, or account recovery. If my mom accidentally locks herself out of Netflix and can’t undo it, I’m *literally* the helpline. That doesn’t scale.
But if there’s genuine interest? Sure, I’ll throw it on GitHub under MIT and call it a day.
## Alternatives That Don’t Suck
If you’re not ready to sideload my slightly-janky APK, here are some solid alternatives that work out of the box:  
1. **BlockSite** (Free + Premium options): Super polished. Great for blocking both platforms and websites, though the free tier pesters you.
2. **Focus Apps** (various): Desperate for an iPhone option? Opal or Serene might work—both offer free trials, though long-term use is $$$.
3. **Digital Wellbeing (built into Android 9+)**: Honestly, 80% of what people need is already in here. It’s just boring and hidden under four menus.
## Lessons from Building for My Mom
Here’s what this project drilled into my brain:  
1. **Most apps are too complicated.** My mom didn’t want metrics, habits, or progress streaks. She just wanted "Facebook to stop being a thing" for most of the day.  
2. **Default settings matter.** The app defaults to blocking 9 AM–7 PM because that’s when she’s scrolling. No one wants to futz around with manual schedules.  
3. **Tech is good, but friction is better.** A great app blocker doesn’t have to be flawless. Sometimes all you need is a speed bump to stop doomscrolling.  
No FAQs here. If you want source code or a sideloadable version, drop a comment on Reddit. Or don’t. Your choice—this isn’t Freedom.
