---
title: 'Building My Dream Video Editor: AI Captions Just Landed'
date: '2026-09-12 20:00:03+08:00'
draft: false
tags:
- indie-hacker
- side-project
- technology
summary: I built my own video editor from scratch. Adding AI captions was harder than
  expected but totally worth it. Here’s how it works.
---

So I’ve been building my dream video editor for the past six months. Why? Because Premiere Pro gives me ulcers and most other editors are either bloated monstrosities or lack features I actually need.

Think lightweight, browser-based, zero bloatware, and no monthly fees. It’s early days, but I just shipped one feature that’s been a game-changer for my little side project: **AI-generated captions.**

Let’s talk about how I added it, why it was weirdly tricky, and what I’d change if I could start over. If you’re also tinkering with AI in video editors, you might save a few hours of frustration.

---

## Why Add AI Captions Anyway?

Captions are *everything* right now—TikTok, Instagram, YouTube Shorts, you name it—fast, catchy videos need text. And people *expect* captions to pop in perfectly, like magic.

But doing it manually? Absolute pain. Even with software like Premiere or Descript, it’s tedious. You edit, tweak timing, fix typos—it’s like customizing subtitles for your Netflix binge. Nobody should spend 20% of their “editing” time moving fonts around.

AI captions to the rescue. Drag video → click one button → watch captions appear. That’s the dream.

---

## The Hard Part: Speech-to-Text on a Budget

Okay, so here’s where I made it harder for myself than it needed to be. The challenge: I wanted it **fast** and **cheap**, without relying on something heavy like Whisper (I’ll explain why in a sec).

### First Attempt: OpenAI Whisper

I started with Whisper because, let’s face it, it’s the gold standard of open-source speech-to-text. Install it locally, throw your audio at it, and you get surprisingly accurate transcriptions. But here’s the issue: **it’s slow AF.**

On my MacBook Air M1, whisper.cpp could churn through a 2-minute clip in about 45 seconds. Not bad… until you realize people expect real-time results for stuff like Instagram stories.

I love Whisper, but for quick-turnaround projects, it’s overkill.

### The Actual Solution: AssemblyAI API

After some trial-and-error (and reading way too many r/sideproject comments), I switched to AssemblyAI’s speech-to-text API. It’s commercial, yeah, but here’s the deal:

- **Setup time:** 10 minutes to get started.
- **Price:** Free for the first 5 hours of processing a month. After that, $0.15/minute.
- **Speed:** Nearly real-time for short clips. I’m talking 10-second transcription speeds for a 1-minute file.

The tradeoff? You’re piping all your video/audio to an external server. If you’re sketched out by uploading client content (or you’re building this for work), this might not be for you.

---

## Integrating with My Video Editor

Here’s a high-level overview of my integration stack. If you want to replicate this for your project, you’ll need three things: **your frontend**, **a simple API backend**, and **access to an AssemblyAI account**.

### 1. Frontend Upload

Users upload a video clip through a React-based frontend (I’m not reinventing the wheel here). The file gets converted to `audio/mpeg` for easy handling server-side.

```javascript
const uploadVideo = async (videoFile) => {
  const formData = new FormData();
  formData.append("file", videoFile);

  const res = await fetch('/api/upload', {
    method: 'POST',
    body: formData,
  });

  return await res.json();
};
```

### 2. Backend: Hitting AssemblyAI

The backend runs an Express server with Node.js. Once the video upload completes, I strip the audio and ping AssemblyAI with their SDK.

```javascript
const AssemblyAI = require("assemblyai");
const assembly = new AssemblyAI("<Your_API_Key>");

async function transcribeAudio(audioUrl) {
  const transcript = await assembly.transcribe({ audio_url: audioUrl });
  return transcript.text;
}
```

Honestly, if you know how to work an API, the hardest part here is error handling. Assembly’s docs are solid though, so you won’t get lost.

### 3. Display in Video Preview

Once transcription’s done, I inject the captions as time-coded overlays on my video canvas. For now, I’m just using `HTMLVideoElement` and plain ‘ol `<div>` captions. Eventually, I’ll switch to a WebGL renderer for fine-tuning animations.

```javascript
const renderCaptions = (captions) => {
  captions.forEach(({ start_time, end_time, text }) => {
    // Map timecodes to on-screen captions
    console.log(start_time, end_time, text);
  });
};
```

Is it polished? Nope. Functional? For my MVP, it’s perfect.

---

## What Didn’t Work Great

Two words: **alignment issues.** Speech-to-text is never 100% perfect, and I ran into fun little things like words appearing half a second off or captions breaking across weird spots.

If you’re serious about captions aligning *perfectly*, you’ll need to preprocess your transcription output. AssemblyAI gives you word-level timestamps, so you could finetune this. I… just didn’t bother (yet).

---

## What’s Next?

- Fancy animations: TikTok-style flying captions, glowing outlines, etc.
- Multilingual support: AssemblyAI supports some extra languages, but I need to figure out UX first.
- Offline mode: I want to reintroduce Whisper for users who don’t want to upload files. The plan? Use it sparingly for power users with high-end rigs.

Your mileage may vary, but if you’re tinkering with video tools, captioning can make or break user experience. Just… don’t over-engineer it. Get captions working. Iterate later.

---

### FAQ

```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why not use Whisper?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Whisper is accurate but slow unless you're running it on a top-tier GPU. For MVPs or projects needing fast turnarounds, cloud APIs like AssemblyAI are faster."
      }
    },
    {
      "@type": "Question",
      "name": "How much does AssemblyAI cost?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "The first 5 hours of transcription are free. After that, they charge $0.15 per minute of audio processed."
      }
    },
    {
      "@type": "Question",
      "name": "Does AssemblyAI work offline?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No, it's a cloud service. If you need offline captions, tools like Whisper or vosk might be better choices."
      }
    }
  ]
}
