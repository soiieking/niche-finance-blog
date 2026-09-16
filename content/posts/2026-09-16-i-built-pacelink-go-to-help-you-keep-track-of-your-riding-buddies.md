---
title: I Built PaceLink Go to Track Your Riding Buddies — Here’s What Happened
date: '2026-09-16 18:00:03+08:00'
draft: false
tags:
- indie-hacker
- cycling
- side-project
summary: How I went from 'this can't be that hard' to 'okay, this is harder than I
  thought' while building a cycling tracker for groups.
---

## Group Rides Are Chaos

I don’t know if you’ve ever ridden with a group of cyclists, but it’s *herding cats on wheels.* Somebody always sprints off the front. Another dude burns out early and gets dropped. And the best part? Nobody knows where anyone is, even though we’ve got more GPS devices than a NASA test lab.

I thought, “There’s gotta be a way to fix this.” Spoiler alert: there kind of is, but it’s not as easy as it looks.

That’s how PaceLink Go started. I wanted a simple way for riders to know where their group was during a ride. No giant screens, no fiddly app that crashes mid-tour, just a basic dashboard (ideally on your bike computer) that says, “Steve is 120 meters behind you. Laura is 1 km ahead.”

## The Build: A Mix of Tech and WTF Moments

### GPS Tracking: Harder Than It Looks

The core feature of PaceLink Go is GPS tracking, but there’s an obvious problem: not everyone has the same gadgets. Some riders are Garmin lifers. Others swear by Wahoo. A few show up with a smartwatch and a prayer. I don’t want to exclude anyone, so I ended up integrating with Strava’s API, because almost everyone syncs there anyway. 

Parsing the live data from Strava turned out to be *both easier and dumber than I expected.*

- **Easier** because their API docs are surprisingly good (shoutout to whoever wrote those).
- **Dumber** because I learned the hard way that Strava’s real-time segments aren’t *actually* real-time. There’s a lag. Sometimes it’s 1-2 seconds, sometimes it’s 10. And when you’re bombing down a hill at 40 km/h, guess what? Everyone’s “position” becomes a guessing game.

Still, "better than nothing" seemed like the right bar to aim for at V1.

### The Tech Stack: Overkill, Then Dialed Back

In my excitement, I initially over-engineered everything. React frontend, Node.js backend, PostgreSQL for storage, Docker containers because… well, everyone’s using Docker, right? Wrong. After wrangling a bloated stack for two solid weeks, I threw half of it in the garbage.

Now it’s barebones:
- **Frontend:** Vanilla JS and HTML (it’s 2026, and yes, this still works beautifully).
- **Backend:** A Flask server to handle API calls. I wanted something lightweight and fast, and Flask + Python absolutely delivered.
- **Hosting:** Render.com free tier. I considered Heroku (RIP affordable pricing) and AWS (too many knobs to turn). Render was up in minutes and didn’t make me regret my life choices.

### Battery Life Matters. A Lot.

This was a curveball. Most cycling group trackers I tested ate phone battery like a TikTok marathon — not ideal when you’re three hours into a ride and need Google Maps for the ride home. I focused hard on keeping PaceLink Go lightweight: no bloat, no constant pings to the server.

Even with these efforts, I’m still getting mixed results. My Pixel 8 lasted a five-hour ride with about 20% left, but an iPhone SE was crying for its charger after three. Optimization is an ongoing battle… and now I get why software teams obsess over this stuff.

## Did It Work? Mostly.

The first real test was my usual Sunday group ride. Eight riders, mixed abilities, trying to survive 80 kilometers without killing each other.

- **The good news:** People *loved* seeing live positions on their bike computers. It immediately stopped one sprinter from disappearing into the horizon.
- **The bad news:** The Strava lag caused some weirdness. Twice, the app announced, “Tim is only 200 meters ahead” when Tim was very much *not* in sight.

Still, the group said they’d use it again. So, I’m counting that as a win.

## What’s Next?

Version 2 will need some serious polish. Better handling of GPS lag, tighter battery performance, and maybe even some Garmin/Wahoo direct integrations if I can figure out their SDKs without losing my sanity. I also want to test this with bigger groups — 15+ riders — to see if it melts under pressure.

But for now, I’m happy I built *something.* It does the job, and my cycling buddies don’t hate me. That’s all you can really ask for in a side project.

## Final Thoughts

If you dream of building your own cycling app, here’s my advice: keep it simple. Focus on one killer feature and nail it. I wasted way too much time on shiny things (Docker setups, frontend frameworks) that didn’t matter. Start small, and just ship something.

Good luck, and if you try PaceLink Go, let me know how badly it breaks. 😉
