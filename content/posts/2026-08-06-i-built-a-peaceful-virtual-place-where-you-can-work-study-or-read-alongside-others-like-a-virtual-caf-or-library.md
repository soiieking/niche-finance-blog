---
title: 'Building a Virtual Coworking Space: The Real Technical Trade-Offs'
date: '2026-08-06T12:00:34+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Building a Virtual Coworking Space: The Real Technical Trade-Offs.'
---

There was a post on r/sideproject this week from someone who built a virtual café. You log in, you pick a table, and you just work, read, or study in silence alongside other people. It’s the ultimate "body doubling" app.
I love the concept. The execution is where nightmares live. 
Building a virtual space is completely different from building a SaaS dashboard. You are handling real-time, continuous media streams. The original poster was hosting a tiny prototype on a $5 DigitalOcean droplet. That is英勇. 
You can maybe cram four simultaneous peers onto a basic DO box before your CPU melts. So how do you actually scale this?
### The SFU Route (LiveKit)
WebRTC is a mess of ICE candidates, STUN servers, and NAT traversal tricks. If you try to code a mesh network—where every browser talks directly to every other browser—good luck. Mesh works for a quick 3-person Google Meet call, but it blows up past that because users will choke on their own upload bandwidth trying to send video to five different peers.
You need an SFU (Selective Forwarding Unit). It receives the media once and routes it to everyone else. LiveKit is arguably the best open-source option right now. 
LiveKit is ready to go out of the box and the Go client ecosystem is solid. The catch? You need a decent server. I run LiveKit on a Hetzner CCX13 ($13.50/month, 4 vCPU, 16GB RAM) and it handles a couple dozen peers easily. I tried it on a cheap ARM box from Oracle’s free tier. I haven't tested this on ARM in production, but the transcription pipeline kept panicking, so I abandoned it.
### WebSockets and a Janky Grid
If you literally just want a grid of faces and a "click to mute" button, you can hack a much simpler stack together. 
Slap a standard WebRTC client on the frontend and use a basic Go server over WebSockets just to pass signaling data and track who is sitting at which virtual table. WebSockets are dead simple to host. Fly.io handles this perfectly and their autoscaling is actually logical for a side project. 
You deploy, scale to zero, and only pay pennies when someone is actually using the room. The problem is security. LiveKit handles token authentication and room management for you. If you roll your own WebSocket signaling layer, you have to build a token validation system yourself. For 90% of people who post in r/sideproject, rolling your own auth state is overkill. You will spend two days debugging it instead of polishing the UI. 
### The Final Boss: Server Egress
The code is the easy part. The hard reality is bandwidth. 
WebRTC is pure peer-to-peer media. It chews through data. One user streaming 720p video at 1.5 Mbps suddenly becomes 150 Mbps of outbound traffic if you have 100 people in the room. 
LiveKit and Fly.io will charge you for that egress. If your virtual library goes viral on TikTok and 500 people try to log in at once, you better have your billing alerts set up. Your $20 credit will vanish in hours. 
Worse, browsers will throttle background tabs. If your user switches from your virtual café tab to Spotify, the browser nukes their media tracks to save RAM. The community around WebRTC proxies is genuinely split on whether you should fight the browser natively or just force users to keep a tiny pop-out PiP window open to maintain the stream.
I think the PiP approach is great for focus apps. But honestly, if your app is meant for "peaceful" studying, do you even need video at all? 
A pure audio-only app—just directional spatial audio—uses 90% less bandwidth and sidesteps all the tab-throttling issues. 
If you really want to build this, skip the video for your MVP. Build a cozy virtual room with ambient audio, let people drop a static avatar on a 2D map, and let LiveKit handle the heavy lifting. You save yourself from the bandwidth bills, your users save battery life, and nobody has to stress about their hair.
