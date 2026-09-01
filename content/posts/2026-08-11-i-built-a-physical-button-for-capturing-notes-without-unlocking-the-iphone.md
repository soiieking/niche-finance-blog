---
title: I Built a Physical Button for Capturing Notes Without Unlocking My iPhone \u2014\
date: '2026-08-11T20:00:13+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding I Built a Physical Button for Capturing Notes Without Unlocking
  My iPhone \u2014.
---

\ Here's What I Learned"
The original r/sideproject thread was a goldmine. OP posted a video of a small, 3D-printed button sitting on their desk. Press it, and a note gets captured to their iPhone — no unlock, no swipe, no opening an app. The comments went exactly where you'd expect: half the room asking "how do I buy this?" and the other half asking "why didn't you just use a shortcut?"
Both groups are right. And that tension is exactly why this project matters.
## The Problem Is Real, Even If the Solution Isn't
Let's be honest: capturing a thought on iOS is still a three-step dance. Unlock, swipe to the right widget, tap the mic, wait for the transcription to catch up. By the time you've done that, the thought is gone. The thread's OP nailed it in their first comment: "I lose about 20 ideas a day to the friction of unlocking my phone."
That's not a niche problem. That's a productivity tax everyone pays.
The obvious answer is Siri. "Hey Siri, note this." But Siri's transcription is still hit-or-miss, and whispering "Hey Siri" in a meeting is social suicide. The button solves a real gap: a physical, always-available input that doesn't require voice or screen interaction.
## The Build: Simpler Than You Think, Harder Than You'd Hope
The hardware is honestly the easy part. OP used an ESP32 (about $4 on AliExpress) with a BLE HID profile. That's the same trick cheap Bluetooth keyboards use — the phone sees it as a keyboard, so no app needs to be open. Press the button, the ESP32 sends a keyboard shortcut, and a Shortcuts automation picks it up.
The setup time is roughly two hours if you've flashed an ESP32 before. Double that if you haven't. The firmware is a fork of the ESP32-BLE-Keyboard library, which is stable but has one known quirk: it occasionally drops the first keystroke after a long idle. OP confirmed this in the thread, and I've seen the same issue on my own builds. The fix is a dummy keystroke before the real one, which adds about 200ms of latency. Acceptable, but not elegant.
The real pain point is iOS Shortcuts. The automation triggers on a keyboard shortcut, but iOS doesn't let you run a shortcut that captures audio without showing *something* on screen. OP got around it by using a text input field with autofocus, but that means the note is captured as text, not voice. If you want voice transcription, you're back to the unlock problem.
## The Community Is Genuinely Split on This
The thread's top comment was brutal: "This is overkill. Just use the Action Button on iPhone 15 Pro." Fair point. The Action Button does exactly this with zero hardware. But it's still on the phone. The whole point of a physical button is that it's *not* the phone. It's a fidget toy that happens to capture thoughts.
Another commenter suggested a $15 Bluetooth shutter button from Amazon. That works too, but the battery life is terrible — about two weeks on a CR2032. The ESP32 build runs for months on a single 18650 cell. Your mileage may vary, but I'd rather recharge monthly than weekly.
The genuinely split part is the transcription pipeline. Some people swear by Whisper API (about $0.006 per minute of audio), others use Apple's built-in dictation. The Whisper route is more accurate but requires a server. OP runs theirs on a Hetzner CX22 ($4.50/month) with Docker, which is fine, but honestly overkill for a note-taking button. A Raspberry Pi Zero 2 W would do the same job for a fraction of the power draw.
## Why This Matters Now
The timing isn't accidental. iOS 17's Shortcuts overhaul made this kind of automation actually reliable. Two years ago, this project would've been a hacky mess of URL schemes and jailbreak tweaks. Now it's a weekend project with a real chance of working.
But here's my honest take: this is a solution looking for a problem for 90% of people. If you're already using Obsidian or Drafts with a quick-capture widget, the button saves you maybe two seconds per note. That's nothing, but it's not life-changing either.
The 10% who benefit are the ones with ADHD, the ones who lose thoughts in the gap between thinking and typing, the ones who've tried every app and still miss the physical act of pressing something. For them, this button is a game-changer. Not because it's faster, but because it's *physical*. It turns a digital habit into a tactile one.
## The Fatal Flaw
The battery. OP's build uses a TP4056 charging module and a 3.7V 18650. That's fine, but the ESP32's deep sleep current draw is around 10µA, which sounds great until you realize the BLE stack wakes it up every 30 seconds to keep the connection alive. Real-world battery life is about three weeks, not three months. OP admitted this in a follow-up comment: "I'm charging it more than I'd like."
The fix is a mechanical switch that physically cuts power when not in use. But then you're pressing two buttons to capture one note, which defeats the purpose.
## FAQ
**Q: Can I use this with Android?**
A: Yes, but it's easier. Android's BLE HID support is more permissive, and Tasker can trigger on keyboard shortcuts without the screen-on requirement that iOS has. Expect a 30-minute setup instead of two hours.
**Q: Does this work with the iPhone locked?**
A: Partially. The button can wake the screen and trigger the shortcut, but iOS will show a notification banner. You can't capture a note entirely in the background without a jailbreak. The screen will flash briefly, which might be a dealbreaker in meetings.
**Q: What's the total cost?**
A: About $15 in parts if you have a soldering iron and a 3D printer. $40 if you need to buy those too. The ESP32 is $4, the button is $2, the battery is $5, and the charging module is $1. The rest is your time.
The thread ended with OP saying they're considering a Kickstarter. I hope they don't. This is a perfect personal project, and turning it into a product would mean dealing with FCC certification, battery safety, and the nightmare of supporting iOS updates that break Shortcuts automations. Keep it as a desk toy. It's better that way.
