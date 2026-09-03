---
title: 'I Save Everything and Never Read It: Here''s the App I Built to Fix That'
date: '2026-09-03 12:00:12+08:00'
draft: false
tags:
- indie-hacker
- productivity
- sideprojects
summary: Built an app to solve my digital hoarding problem. Here’s what worked, what
  didn’t, and why this habit even matters.
---

## The Problem: Saving Is Easy, Reading Is Hard

I hoard articles, tutorials, and project ideas like an apocalypse prepper hoards canned beans. Pocket, Notion, Instapaper — they’re all crammed with good intentions I'll never act on. Sometimes I even forget *why* I saved something. 

A real commenter on r/sideproject captured this perfectly: “I bookmark posts about productivity while procrastinating. It’s a disaster.” That’s the energy we’re working with here. 

So I built something: an app that not only saves links but forces me to actually engage with them—or deletes them after a deadline. Think of it like Marie Kondo for your digital clutter.

## Why This Matters Now

First, digital overwhelm is real. According to Pocket (yes, the company literally enabling this madness), the average user saves 20 links every week but reads less than half. That’s a lot of cognitive baggage. And yeah, maybe I should just "have more discipline," but you know what’s easier? Automating stuff.

Second, there's a gap in the productivity app ecosystem. Tools like Obsidian or Mem work great *if* you already have a workflow. But for people drowning in content, these tools aren’t solving the root problem: deciding what to keep and when to use it.

This app isn’t for hardcore organization nerds. It's for people like me: chaotic savers who need external pressure to act.

## How It Works: My App’s Simple Gambit

Here’s the pitch: You save a link, assign it a tag (optional), and choose a deadline, e.g., “7 days to read or delete.” The app then hits you with reminders. If you miss the deadline? It’s gone. No rescue, no whining.

- **Tech Stack**: Built with Svelte (because I enjoy my sanity), Firebase Firestore for unlimited free-tier notes, and Vercel for hosting. It’s embarrassingly low-budget. 
- **Why the 'nuclear option'?** Scarcity creates urgency. A link that could vanish is way more motivating than one sinking to the bottom of an endless folder. 
- **Why not extend Pocket or Notion?** I tried. Their APIs made me want to scream. Plus, this needed to be opinionated—built for deleting, not organizing.

## Early Results: Does It Actually Work?

Yes… kinda. It won’t magically make you read *everything*, but it has forced me to prioritize. My backlog went from 487 saved links to 82 in three weeks. 

Here’s the deal: The app shines for short-term reads. Tutorials, industry news, things you’d forget about otherwise? Perfect. But deep-dive PDFs and giant research reports? Still clunky. I’m debating adding “snooze” functionality.

Another win: the UX. Simplicity landed well with early testers (friends from r/sideproject). One said, “It’s like a to-do list but meaner. I like it.”

## What Didn’t Work (Yet)

Surprise, building apps doesn’t make you a better person. I still save too many obscure GitHub repos I’ll never clone. The app also isn’t great for non-link collection: PDFs, images, weird TikTok links. I might need OCR or integrations, but that’s scope creep for now.

Monetization is another question mark. I’m not planning to charge yet, but long-term hosting (especially if I hit even mild scale) is a lurking cost. Those Firebase free-tier limits? Yeah, they’re gonna bite eventually.

And finally, let’s address the elephant in the room: *would people even use this long-term?* The digital hoarding problem is inherently psychological, not just technical.

## Why I’m Sticking With It

Two reasons:  
One, it’s working well enough for me, which was the point all along. Even if nobody else adopts it, I’ll keep using it because it’s genuinely saving me from digital exhaustion.  
Two, I’d rather ship something scrappy and imperfect now than wait for a unicorn of functionality later. Launch-and-learn beats analysis paralysis.  

If there’s a moral here, it’s this: build tools you’ll actually use. Even if your user base is only “you.”

---

### FAQ

#### How’s this different from existing tools like Pocket?
Pocket is for *collecting*. My app is for *forcing action*—using those links, or losing them. No option to hoard endlessly. It’s a feature, not a bug.

#### Any plans for mobile apps or extensions?
Not yet. The MVP is a web app optimized for mobile browsers. If the demand’s there, I’d consider iOS/Android wrappers.

#### Is this app available to try right now?
I’ve soft-launched it! DM me on r/sideproject if you want the link. I’m keeping it small to squash bugs before pushing wider.

---

That’s it. If this resonates, drop your email and I’ll keep you posted on where this goes. Or just vent about your own hoarding in the comments—I get it.
