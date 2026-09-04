---
title: I Made an App for Live Push-Up Battles Against Strangers. Here's What r/SideProject
  Thinks.
date: '2026-09-05 06:00:04+08:00'
draft: false
tags:
- indie-hacker
- fitness-tech
- side-project
summary: A weird little app pits you against strangers in livestreamed push-up battles.
  Will anyone actually use this? Reddit has thoughts.
---

## The Wild Idea: Push-Up Battles with Random Strangers

This is either genius or completely insane: an app where you and some random stranger livestream your push-ups, with the app counting reps using your phone camera. It’s like Chatroulette, but for sweaty people.

The big hook here is the automation—your camera tracks reps for both you and your opponent. No cheating. No lying. Just pure upper-body struggle on display for a faceless enemy to crush or be crushed by. I saw this one pop up on r/SideProject, courtesy of user u/updog_downcat, and yeah, the comments were *exactly* what you'd expect.

Let’s break down the feedback from the sideproject crowd. Spoiler: nobody held back.

---

## “Innovative but Niche” — The Core Problem

Reddit’s first reaction? This thing is hyper-specific. “How many people want to sweat their faces off on camera for virtual glory?” asked u/devronaut. Fair. Fitness tracking is already oversaturated (Apple Watch, Strava, MyFitnessPal), and this app’s use case—real-time competition—is undeniably niche.

u/dropshipper90 chimed in: “I can see CrossFitters or hardcore types loving this, but for the average person, it’s overkill.” Tough crowd, but they have a point. Most people don’t even want to answer FaceTime calls, let alone livestream their push-up form to a stranger.

But! This taps into that primal desire for competition. Apps like Zwift and Peloton thrive on turning fitness into social bragging rights. The question is whether this app can carve out similar appeal with fewer users and a much simpler concept.

---

## The Tech: Does It Even Work?

Here’s the make-or-break question: Is the rep counter actually accurate? If you’re basing everything off your phone’s front camera, there’s a lot that can go wrong. Bad angles, crappy lighting, or worse—someone gaming the system by just nodding their head up and down like an unhinged pigeon.

u/not-a-real-cat commented, “I’d be surprised if this works consistently without a depth cam. Have you tested against the iPhone 14’s LiDAR sensor?” Apparently not—u/updog_downcat admitted the MVP is purely 2D phone vision at this stage, with occasional hiccups on range and false reps.

That said, they built it in OpenCV, so tweaking algorithms isn’t impossible. But yeah, if you’re expecting Apple Fitness+ levels of polish, this ain't there yet. “Ship it now, fix later” vibes all around.

---

## Who’s Paying For This?

Ah yes, monetization. Naturally, Reddit went straight for the throat here. “How are you planning to make money—ads between sets? A subscription for sweaty strangers?” u/StanApps joked. 

The creator responded with a freemium pitch: free battles for everyone, but premium lets you issue friend challenges and unlock leaderboards. They’re also flirting with fitness brand sponsorships (imagine Under Armour ads popping up after you lose at 15 push-ups).

Great in theory, but freemium fitness apps are a graveyard. As u/platonicmind put it, “Peloton hemorrhaged cash *with* a hardware moat. You’re selling push-ups.” That’s brutal. Accurate though.

---

## Potential or Pipe Dream? 

Look, is this the next billion-dollar app? Probably not. Even the creator admits it’s a side project with an audience issue: narrow appeal, high churn potential. But does that mean it’s worthless? Nope.

u/kalimando hit the nail on the head: “This feels less like a business and more like a viral stunt. But stunts can go far if you play them right.” If nothing else, push-up battles are weird enough to get noticed. TikTok, anyone?

---

### Key Takeaways for Builders

1. **Test early, test cheap.** The app has rough edges, especially in rep counting, but OpenCV makes testing + iteration fast.
2. **Find your superfans.** Hardcore fitness nuts are the likely audience here. Niche yes, but passionate niches work.
3. **Virality > features.** Lean into the ridiculous. Less is more when chasing trends.

The app isn’t perfect (or even necessary), but its ambition is refreshing. Whether or not it takes off, you gotta respect the hustle.

---

## FAQ

### **How does the app count push-ups?**  
It uses OpenCV to track your body movement via the phone's camera. Accuracy depends on lighting, camera angle, and pose consistency. No LiDAR (yet). Expect occasional false reps or misses.

### **Is there a privacy risk with this livestreaming?**  
Kind of. There’s no facial recognition or permanent recording, according to the dev. But yeah, you’re still broadcasting yourself doing push-ups to strangers, so... grain of salt.

### **What’s the monetization plan?**  
The dev plans to offer free use with a premium subscription for friend challenges, leaderboards, and other features. Sponsorships from fitness brands are also in the mix.

---
