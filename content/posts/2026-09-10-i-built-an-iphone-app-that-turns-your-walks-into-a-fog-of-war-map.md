---
title: I Built an iPhone App That Turns Your Walks Into a Fog-of-War Map
date: '2026-09-10 04:00:04+08:00'
draft: false
tags:
- indie-hacker
- apps
- side-projects
summary: How my app gamifies walking with fog-of-war maps, and what I learned while
  building it.
---

## The Idea: Gamifying Walks with Fog-of-War

This project was born because I get bored easily. Running or walking on the same old routes feels like chewing gum that’s already lost its flavor. But what if every walk could be a game? Enter: fog-of-war maps.

If you’ve played video games like *Civilization* or *Starcraft*, you know what I’m talking about. Parts of the map are blacked out, and you “reveal” areas by exploring. My app does this for the real world. Wherever you walk, the map slowly uncovers. Simple idea, right? Sure—but building it was a whole other mess.

## How It Works

The app is brutally straightforward. You fire it up, it grabs your GPS location, and it overlays a fog-of-war effect on your area. As you move around, the app clears the fog, leaving a trail of where you’ve been.

I linked Apple’s Core Location API to a custom tile-based mapping system. Core Location gives you GPS data; the tile engine handles the fog effects. The hardest part? Making it not suck battery. GPS-based apps, especially with constant updates, suck juice like there’s no tomorrow.

Here’s where I hit my first wall: map tiles. Apple Maps doesn’t give you raw tiles to play with, and Google Maps’ free tier throttles you after like 100,000 requests. I wanted offline functionality anyway, so I pulled tiles from OpenStreetMap using Mapbox. (God bless Mapbox—they’ve got a free tier that’s actually usable.)

Add some lightweight fog rendering, a little caching magic, and voila: a working app.

## The Tech Stack: Overkill or Just Right?

Here’s the full stack:  
1. **Frontend:** Swift for iOS, because I hate React Native’s quirks.  
2. **Maps:** OpenStreetMap tiles via Mapbox (free tier for dev/testing).  
3. **Backend:** None. Everything’s local—no server sync, no user accounts. It's a “just works” kind of app.  

Could I have made this even simpler? Sure. Some folks in r/sideproject suggested I ditch the fog effect altogether and just track routes like Strava. But that felt boring to me. No offense to Strava—it’s great for people who want performance metrics. This app is for people who just want to explore more and have fun doing it.

And the main downside to keeping everything offline/local? No cloud backups. If you lose your phone, you lose your progress. Trade-offs.

## Why Build This?

Honestly, this was as much for me as anyone else. I wanted to learn app dev, but not by building Yet Another To-Do List™. Also, I liked the idea of gamifying something as mundane as walking. Walking is the easiest exercise there is, but most people still skip it. Gamification feels like a cheat code to fix that.

Shoutout to a comment on r/sideproject that nudged me toward this: someone mentioned their kid walking more thanks to Pokémon GO. That stuck with me. Fog-of-war scratches a bit of that same itch.

## What Went Wrong (and Right)

**First, let’s talk about a huge L:** iOS’s background location updates. Apple throttles background tasks hard, and apps get killed silently if you’re not careful. This means your fog stops rendering if you lock your phone during a walk. I worked around this by telling users to keep the app open while walking. Not ideal, but it works for now.

Is this something I can fix with more dev time? Maybe. Apple’s location permissions changed significantly around iOS 15. Newer APIs are more power-efficient, but I haven’t totally wrapped my head around them yet.  

**What went right:** The app is so lightweight that even older iPhones (I tested on an iPhone 8) run it fine. No animations, no 3D rendering—it’s just 2D tiles and a simple fog overlay. CPU usage barely blips, which I’m proud of.

## Is This Useful or Just a Fun Toy?

Honestly? Could go either way.

If you're into hardcore fitness tracking, this is useless to you. No heart rate monitor, no fancy analytics. For that, just use Strava or Apple’s Fitness app. But if you’re like me and you just need a new incentive to lace up your shoes, this might do the trick. My personal weekly walking distance is up 40% since I started testing.

Also, random note: people with kids seem to love this. One user on Reddit said their 6-year-old declared themselves “the map king” after clearing their entire neighborhood. So apparently it’s a hit with smaller humans, too.

## What’s Next?

Honestly? I haven’t decided. The app’s free right now, but if I see decent traction, I might add some IAPs (in-app purchases) for features like saving progress across devices. But that requires a backend, and I’m allergic to spinning up AWS servers unless I absolutely have to.

I’d also love to hear ideas for new features. Someone on r/sideproject suggested connecting this to Apple HealthKit for step syncing. Could be cool, but I worry it’d start complicating the “simple and fun” vibe.

Got suggestions? Hit me up.

---

### FAQ

#### Does the app drain your battery a lot?
Not as much as you’d think! By keeping everything local and limiting GPS updates to once every few seconds, I kept usage low. In my tests, a 1-hour walk used ~6% battery on an iPhone 13 Pro.

#### Can I use the app offline? 
Yup. Maps are cached locally once downloaded. As long as you don’t clear the cache, you can explore most areas without worrying about data.

#### Will this come out on Android?
Maybe someday. For now, I’ve focused on iOS to keep things manageable. Android has a more fragmented ecosystem, and I didn’t want to juggle device-specific issues during MVP development.
