---
title: How to Build a Multi-Layer Video Editing App (What Worked for Me, What Didn't)
date: '2026-09-04 20:00:04+08:00'
draft: false
tags:
- indie-hacker
- video-editing
- side-projects
summary: Learn how to build a multi-layer video editing app without pulling your hair
  out. Tech stack, pitfalls, and lessons from r/sideproject.
---

## Why Would You Even Build This?

Before we get too deep, ask yourself: what’s your use case? Most people don’t need a multi-layer video editor unless they’re building the next TikTok, adding caption/subtitle overlays, or doing fancy compositing. If you're trying to compete with Adobe Premiere Pro—stop. Seriously. Don't.

But if you're chasing something lightweight and web-first, let’s talk.

On [this post in r/sideproject](https://www.reddit.com/r/sideproject/comments/.../), someone shared that they were trying to build a Chrome-based app for quick cuts and overlays. Another commenter mentioned using FFmpeg as the backend. Both good, but incomplete.

Here’s how I would approach it.

---

## The Stack: Keep It Simple, Until You Can’t

You're essentially building three things:

1. **A UI users won't rage-quit.** Think React or Svelte for the frontend. Gorgeous UIs don’t matter if they're slow, but terrible UIs will kill your project. Full stop.
2. **An engine that can process video frames like a beast.** This is where FFmpeg shines. Pick your battles here—FFmpeg isn’t perfect, but it’ll save you a year of work.
3. **Layer management: AKA, what’s sitting on top of what, when.** This is the tricky part. If you can't handle alpha channels and transitions between layers, your app is toast.

### 1. Frontend: React DOM vs. WebGL/CANVAS

A lot of people will default to React for the UI—and it’s a good choice if your app is fairly simple. But for multi-layer video editing? You’ll run into trouble if your frontend doesn’t leverage WebGL or at least `<canvas>`. Why?

Because DOM elements (like `<div>`s) are slow for rendering videos frame-by-frame at 60fps. For rudimentary previews, it *might* hold up. But for actual animation-heavy editing, move to WebGL.

- **Tool to check out:** [PixiJS](https://pixijs.com/). Your layers live as textures on sprites. Decent learning curve, killer performance.
- **When DOM works fine:** Simple overlays, trimming, static animations.

Run this benchmark before committing to your tech:

```shell
$ npm install react-performance-test
$ npm test --render-video-layer
```

### 2. Backend: FFmpeg vs Actually Crying

FFmpeg has been around forever. It’s written in C, which means fast—but not always modern. Wrap it via Node.js if you need to interact with it from your web app, like this:

```bash
$ npm install fluent-ffmpeg
```

Simple FFmpeg command for stacking two layers:

```shell
ffmpeg -i background.mp4 -i overlay.png \
-filter_complex "[1:v]scale=iw/2:ih/2[scaled];[0:v][scaled]overlay=W-w:H-h" \
-output output.mp4
```

Let’s break that down:

- `scale=iw/2:ih/2`: Shrinks your overlay to half its original size.
- `overlay=W-w:H-h`: Sticks your overlay in the bottom-right corner.
- **Gotcha:** FFmpeg doesn’t do live previews. That’s why your frontend/WebGL sprint matters even more.

You can also look into GPU-accelerated rendering using FFmpeg with CUDA/NVIDIA support, but expect bugs. I've had mixed results.

### 3. Handling Layers: The Hard Part

Here’s where 80% of your time will go: layering. You’ll need:

- **Z-index control**: Users want to reorder elements. Keep this simple on the frontend (drag-and-drop), complex on the backend.
- **Alpha/transparency management**: PNGs and green-screen effects require transparency. FFmpeg can handle chroma-keying, but it’s janky:
  ```bash
  ffmpeg -i input.mp4 -vf "chromakey=green:0.1:0.2" output.mp4
  ```
- **Timeline snapping**: Resist the urge to write your own. Go steal from [react-timeline](https://www.npmjs.com/package/react-timeline) or similar.

One Reddit commenter mentioned using Three.js for 3D layering. Cool idea, but probably overkill unless you’re doing Blender-lite.

---

## Deployment Tips: It’ll Eat RAM, So Get Prepared

If you’re running this in production, know that video rendering = **computational hell**. AWS or DigitalOcean can get super expensive if you’re not careful. I’d recommend:

- **Hetzner**: Dirt-cheap instances with decent CPUs. With a CX31 (2 vCPU, 8GB RAM) at $5/month, you can handle most tasks.
- **Fallback plan:** Limit video to 720p editing. It's enough for casual users.

Dockerize your setup for scaling:

```Dockerfile
FROM node:16
RUN apt-get update && \ 
    apt-get install -y ffmpeg
WORKDIR /app
COPY . .
RUN npm install
CMD ["npm", "start"]
```

---

## Bugs You’ll Hit (And How To Dodge Them)

1. **Out-of-Sync Layers:** 
   Happens when your backend (FFmpeg) and frontend don’t coordinate frame rates. Check FPS at both ends:
   ```bash
   ffmpeg -i input.mp4
   ```
2. **Render Times Suck:** 
   Rendering takes forever if you’re not leveraging GPU acceleration. Test CUDA drivers or move to cloud rendering.
3. **User Rage at Crashes:** 
   Save user progress every step of the way. LocalStorage is a hacky crutch if you can't write it to the server yet.

---

## FAQ

### Why not just use Adobe Premiere Pro or Final Cut Pro?

Because we’re hackers and indie devs. And let’s face it—Adobe doesn’t fit into a browser by default. Building something for casual creators (TikTok, reels) is way different than competing with Hollywood-level tools.

### Can this work in a serverless environment?

Not ideally. You’ll bottleneck at FFmpeg processing. A VM with persistent storage is better. If you *must* go serverless, you’ll need to optimize down to seconds and eat higher costs.

### Does FFmpeg support WebAssembly?

There’s an [FFmpeg WASM port](https://github.com/ffmpegwasm), but it’s experimental. I wouldn’t rely on it yet unless your app is strictly client-side.

---

Building a multi-layer video editor is chaos, but it’s doable with the right stack. React (or PixiJS) for the UI, FFmpeg for the grunt work, and good ol’ Z-index logic for layers. The devil’s in the performance details—happy coding!
