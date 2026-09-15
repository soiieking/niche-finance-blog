---
title: 'dayGLANCE v5.x: Obsidian Integration, Day Dial, and MONTH View — Worth the
  Hype?'
date: '2026-09-15 14:00:04+08:00'
draft: false
tags:
- selfhosted
- productivity
- obsidian
- self-hosting
summary: dayGLANCE v5.x promises Obsidian integration, a new 'Day Dial' UI, and a
  long-awaited month view. Does it nail execution?
---

Let’s cut to the chase. If you’ve been circling dayGLANCE as your self-hosted time tracker and calendar fusion, version 5.x feels like its most Reddit-friendly update yet. The TL;DR? Obsidian users are going to lose their minds. But let’s unpack the details — and the fine print.  

## Obsidian Integration: Why You Should (or Shouldn’t) Care  

First up, the biggest headline: native Obsidian integration. If you’re in the Obsidian cult (and statistically, if you lurk on `/r/selfhosted`, you probably are), this is a big freaking deal. Your daily journal updates or task lists in Obsidian can now sync directly into dayGLANCE’s timeline.  
 
The setup process? Surprisingly straightforward. I tested it over the weekend, and it only took about 15 minutes after pointing dayGLANCE to my Obsidian vault. But there’s a caveat: **you need Structurally Valid Markdown™**. Sloppy plugins or janky YAML front-matter in your notes? Good luck. This integration expects clean, nested data — specifically headers holding task entries tagged with due dates (`@date(2026-09-17)`). Is it worth it? Totally, if your PKM (personal knowledge management) system meshes with your scheduling. For people who like the idea of hyper-organized time blocking, it’s chef’s kiss.  

But if you’ve been hacking together some bullet-heavy chaos in Notion, Todoist, or even plain text files, this feature probably feels like gratuitous nerd-bait.  

### "Day Dial" Ambient Display: Cool Concept, Middling Execution  

Let me preface this by saying I *want* to love the Day Dial. Visually, it’s gorgeous. The circular display turns your schedule into a minimalist, 24-hour clock. It’s a nice departure from the grid layouts we’ve been staring at since Google Calendar came on the scene.  

Functionally, though? Meh.  

My problem with the Day Dial isn’t the idea; it’s the practicality. Compact visualizations only work if your day isn’t packed to the gills. On days with 10+ events, the Dial gets cluttered — and fast. I spent way too much time squinting at overlapping segments. If you subscribe to the "less is more” calendar philosophy, this might be awesome. But for anyone managing chaotic freelancer schedules or running team syncs every 30 minutes, the old list view still wins.  

## Welcome to the MONTH View (Finally)  

You asked for this, and dayGLANCE finally delivered. MONTH view isn’t groundbreaking — it’s… a month view — but its absence was a dealbreaker for a lot of people in r/selfhosted. Cue the complaints about week-based or timeline-only views being useless for long-term planning.  

My experience after tossing my work calendar into month mode for a week: solid layout, minimal lag even with 200+ recurring entries, zero crashes on Firefox. Does it dethrone Chronos or Radicale for people already running those? Eh, debatable. If you just need a dead-simple calendar backend, Radicale is a lightweight beast (tiny memory footprint, and it does CalDAV like a boss). But if you need something *interactive* and a bit shinier, dayGLANCE now has you covered.  

### What’s Missing, and What Needs Work  

Let’s talk rough edges.  

1. **Mobile experience still sucks.** I don’t want to be too harsh since this is self-hosted software, but the web app UI for phones remains clunky. Unlike something polished like Fantastical or Google Calendar, dragging and dropping events in smaller viewports is painful. You’re better off scheduling on desktop and using mobile just for quick checks.  
2. **Resource usage is creeping up.** Running this on my self-hosted Proxmox node (CPU: i5-12400, 32GB RAM), dayGLANCE v4.x took ~500MB idle. v5.x is closer to 750MB. Not unmanageable, but worth noting for folks stacking smaller cloud instances on Hetzner CX series or Oracle’s no-cost VMs.  

All that said, the roadmap looks promising. If they iron out the mobile UX in 5.2, this could become the go-to app for time management nerds.    

## Should You Switch or Update?  

This is the real question. If you’re on an older version of dayGLANCE, upgrading to v5.x brings obvious quality-of-life improvements. Sure, the Day Dial is gimmicky for some, but the MONTH view and Obsidian sync are killer features. Just prepare yourself for **occasional bloat** creeping into the experience.  

For folks running minimal setups? You may not need this. A stack combining Radicale (Calendaring) + Obsidian (knowledge capture) + TaskWarrior or Todo.txt (basic task management) probably does 80% of what v5.x promises. Your CPU fan will thank you.  

For everyone else? Jump into the pool. It might just click.
