---
title: "Building a 3D Map Poster Maker: Three Rendering Stacks Compared"
date: 2026-08-05T08:00:28+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "A deep dive into the three best tech stacks for building a topographical map poster generator with rotating video output."
---

Saw a post on r/sideproject this morning where a dev built a free 3D map poster maker. You pick anywhere on Earth, it renders the topographical terrain, and it spits out a rotating video file. It’s a ridiculously cool concept that sits right at the intersection of "useful personalized gift" and "geeky art piece."

But reading through the comments, the thread immediately derailed into the weeds of how exactly you’re supposed to render and export this stuff at scale without lighting your server billing on fire.

I’ve spent an embarrassing amount of time messing around with geospatial rendering pipelines. There are three real ways to build this, and they all have fatal flaws depending on your budget and patience.

## The Headless Browser Route

A few commenters suggested just spinning up a Node.js Puppeteer script, loading up a MapboxGL canvas, and recording the screen via the `puppeteer-screen-recorder` npm package. 

I love this tool but it has one fatal flaw: headless Chromium is a notorious RAM hog. If you want a smooth 1080p, 60fps rotating video without dropped frames, you need to dedicate at least 4GB of RAM per concurrent render. 

Running this on DigitalOcean will cost you $48 a month for a basic droplet that handles maybe two users at a time before the OOM killer starts swinging. Overkill for most people. If you go this route, spin up a Hetzner CX22. You get 4GB of RAM for about €4.50 a month. 

## Native Python with GeoTIFFs

The second approach is ditching browser-based WebGL entirely. You pull raw Digital Elevation Model (DEM) GeoTIFFs from the USGS API, pass them through something like GDAL, and use Python’s `matplotlib` to build a 3D surface plot. For the video export, you sync the camera angle rotation frame-by-frame to `ffmpeg`.

This is by far the cheapest method to run. You can process this on a $2/mo shared CPU instance and 100% CPU utilization won't even break a sweat. 

But the output looks like absolute garbage without serious tweaking. Matplotlib’s default 3D shading looks like a Windows 95 screensaver. You’re going to spend weeks tweaking custom colormaps and adding hillshade algorithms to make it look like a premium $40 poster instead of a middle-school GIS project.

## Cloud Game Engines (The r/sideproject Favorite)

The original poster mentioned they were actually rendering the video frames in Unity using Unity Cloud Builds. 

Honestly, this is the best looking option but the most brittle to deploy. If you do this, skip Docker entirely and use Podman. Docker’s networking defaults will fight you aggressively on UDP ports, which some engine telemetry and licensing checks use. Podman’s rootless setup maps localhost ports more cleanly for this specific workload.

The other issue is licensing. I haven't tested this on ARM-based Mac or Linux instances yet, so your mileage may vary, but deploying a headless Unity instance as a web backend is an absolute nightmare if you don't want to hardcode your Pro license keys into a Docker image sitting in a public registry. The community is genuinely split on whether doing Unity headless cloud rendering is a stroke of genius or a massive future maintenance debt.

## The Actual Bottleneck

If you build this, the real bottleneck isn't the 3D rendering. It's the exponential API cost of the underlying map data.

If you use Mapbox or Maptiler for your terrain tiles, every time a user zooms into a custom valley or mountain range, the server fires off dozens of raster tile requests. Your free tier of 50,000 requests a month will evaporate in three hours if a post hits the front page of Hacker News. 

Cache your tiles locally. Seriously, set up a reverse proxy with Redis to catch and serve those DEM terrain requests, or you will bankrupt yourself before a single user pays for a poster.