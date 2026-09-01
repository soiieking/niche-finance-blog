---
title: 'Turn Hand Gestures Into Electronic Music: A r/sideproject Breakdown'
date: '2026-08-04T04:53:11+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Turn Hand Gestures Into Electronic Music: A r/sideproject Breakdown.'
---

Saw a project on r/sideproject this week that made me stop scrolling. A dev built a web app that uses your webcam to track hand movements and translates them into electronic music. No keyboards, no MIDI controllers, just you flailing around in your room looking like an orchestral conductor having a meltdown.
## The Build: MediaPipe, Web Audio, and a Prayer
The stack is surprisingly standard for a web-based edge computing project. MediaPipe Hands for the skeletal tracking, feeding data directly into the Web Audio API to generate sounds in-browser. 
No backend server. No Cloudflare Worker. No $20/month DigitalOcean droplet burning a hole in your pocket. It runs entirely client-side. You load the page, grant camera permissions, and start making weird ambient noise. Running locally also means zero latency—a absolute must for live audio, since a 200ms delay between your finger movement and a synth stab will ruin the vibe instantly.
But this architecture has one fatal flaw. 
## The CPU Thermometer
Inference in the browser is heavy. MediaPipe is optimized, but running real-time 21-point hand tracking at a steady 60fps while simultaneously synthesizing audio is a recipe for a laptop melting down.
One r/sideproject commenter, u/building_blocks_99, pointed out exactly this: "Audio glitches and visual dropouts happen if your machine is already struggling to keep Chrome running."
They’re 100% right. Audio dropouts aren't just annoying; they’re project-killers. The creator acknowledged the bottleneck and noted they had to throttle the camera feed to 30fps on mid-range laptops just to keep the DSP from tearing. If you run this on a 5-year-old ultrabook, your framerate will tank, and the audio will sound like a broken robot.
## The Skrillex Scrub
The coolest tech detail is how they mapped the axes. X controls pitch. Y controls a low-pass filter cutoff. Pinching your thumb and index finger acts as a trigger for an arpeggiator. 
But I love that they resisted the urge to over-engineer. 
uossil linked to a competing gesture-synth repo in the thread that tracks full-body skeletal movement. It's totally overkill for most bedroom producers who just want a fun MIDI lick. Hand-tracking is the goldilocks zone for expressive control. Managing 21 points on two hands is mentally digestible. Tracking a full 33-point body model is physically exhausting and spreads your intention too thin. Your hands are already how you play physical instruments. Your elbows aren't.
## Where It Goes From Here
Right now, it’s a toy. A highly addictive toy, but a toy. 
The obvious next step for the creator is MIDI output routing. If they could pipe the MediaPipe coordinates to a virtual MIDI port—like-loopMIDI on Windows or IAC Driver on Mac—users could map the gestures to their actual DAWs. Hooking it up to Ableton or Logic changes it from a browser toy to a viable live-performance rig. 
As u/audio_synth_dev pointed out in the thread: "Browser synth sounds are fine for a demo, but the second I can route this MIDI into my Serum VST, this is a $30 plugin." 
I haven't tested this proxying approach locally yet, so your mileage may vary, but routing browser MIDI out usually hops through WebMIDI API. The catch is that Chrome's native MIDI support is famously spotty across OS versions. If the dev cracks serverless MIDI routing without forcing users to download a companion desktop app like Electron, it'll be huge. They just need to watch those audio threads. Drop the buffer size too low, and you're back to clipping. Drop it too high, and your gestures are playing yesterday's notes.
