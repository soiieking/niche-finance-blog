---
title: 'Building an Eye-Tracking Cursor with Just a Webcam: Cool or Overkill?'
date: '2026-09-10 18:00:03+08:00'
draft: false
tags:
- indie-hacker
- technology
- side projects
summary: Experimenting with turning any plain webcam into an eye-tracking dream (or
  nightmare).
---

So, the idea of using a plain webcam to control your cursor with your eyes? It’s one of those “sounds simple, turns into a mess” kind of projects—and I say this as someone who both loves and regrets going all-in on it. If you’re here for the TL;DR: it works, it’s cool, and no, I wouldn’t use it for anything serious.

Let’s break it down.

## Step 1: What Even Is This?

Someone on r/sideproject described it perfectly: “It feels like magic for 5 minutes until you realize how annoying it is to accidentally blink in the wrong place.” They weren’t wrong. The tech here is deceptively simple. The webcam detects your face, then narrows in on your eye movements. Combine that with some lightweight math, and you’ve got gaze tracking that translates eye movement into cursor movement.

It sounds like a glorified science fair project—and it kind of is—but it’s also pretty damn fun. There’s no special hardware required, no $200 Tobii tracker. Just your $20 Logitech C270 off Amazon and some code.

## The Tools: OpenCV + Python = Where I Started

I kicked it off with the basics: OpenCV and Python. OpenCV has been the go-to for years when it comes to computer vision. It’s a bit of a Frankenstein’s monster (C++ under the hood, clunky Python API at the top), but it gets the job done. Specifically, I used the `cv2.CascadeClassifier` for face and eye detection and some basic NumPy math to calculate gaze direction. This was all running on Python 3.11, in case anyone cares about versions.

Here’s where I ran into my first headache: CPU usage. On my 2021 MacBook Air (M1), the script was eating 40-50% of CPU at all times, just running the webcam analysis. Expect your laptop fans to scream if you’re not on ARM. And forget running anything heavier in parallel.

For context, the raw tracker code ran at ~20-30 FPS on 720p webcam input. Not bad, but you’d notice the occasional lag—especially compared to the buttery smoothness you’d get on dedicated eye trackers. This might be a dealbreaker for anyone trying to use it in live production (accessibility tools? forget it).

## The UX: Cool Until It’s Not

Here’s the thing no one talks about: your eyes are fidgety little bastards. Even when you think you’re holding your gaze still, there’s micro-movement. And a naïve implementation like mine maps all these tiny movements directly into cursor adjustments.

The result? A cursor that feels like it’s on a sugar rush. I tried stabilizing it with some Kalman filters (OpenCV has a library for this), but then I started losing accuracy in quick transitions. Trade-offs everywhere. 

So, unless you’re working on some hyper-customized use case where lag doesn’t matter, this ends up feeling more like a toy than a tool. I played “Can I Close a Browser Tab with Just My Eyes?” for about 10 minutes before rage-quitting.

## Alternatives: Should You Even DIY This?

If you just want working eye tracking and don’t care about the process, there are pre-built packages like GazeR or PyGaze. They abstract away most of the math. In my testing, PyGaze was solid, but it *requires calibrated lighting to shine*. It’s not gonna work well in your dim bedroom at midnight with one uneven desk lamp.

On the hardware side, Tobii makes actual eye-tracking devices that work out of the box, often with their own SDKs. But they’re pricey—starting at $199+ for entry-level stuff—which kind of defeats the indie-hacker “let’s make this cheap” spirit.

## Final Thoughts: Should You Build It?

Honestly, unless you’re bored on a weekend or obsessed with nerdy UX experiments, I’d skip building this. It’s *fun to tinker with*, but anything beyond a short demo is more hassle than it’s worth. 

If you *do* go for it, here’s the roadmap:
- Use a low-res video feed (480p) to keep CPU usage manageable.
- Smooth out gaze tracking with Kalman filters or exponential moving averages.
- Test lighting conditions like your life depends on it. (Spoiler: It does.)

Would I do this again? Maybe. But I’d set clearer expectations and not assume it’ll blow my mind. Sometimes it’s enough just to finish something, even if the end result lives in `/projects/abandoned`.

---

## FAQ: Eye-Tracking Cursor with a Webcam

### Does this work with online meetings or screen sharing?

Nope. The CPU load will make Zoom crash faster than you can say “resource hog.” It’s best as a standalone tool for single-screen interaction.

### Can this replace a Tobii eye tracker?

Not even close. A Tobii device is *wildly* more accurate and lag-free. This is duct tape, not a finished product.

### Any recommendations for beginners?

Start with OpenCV. You’ll learn a ton, and it’s well-documented. Keep your expectations low. Aim for “cool science project,” not “ready for app store.”
