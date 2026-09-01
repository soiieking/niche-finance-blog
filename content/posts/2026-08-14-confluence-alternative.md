---
title: Ditching Confluence? Here's What r/selfhosted Actually Runs Instead
date: '2026-08-14T00:00:41+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding Ditching Confluence? Here's What r/selfhosted Actually Runs Instead.
---

I get it. You saw that Confluence license renewal and felt a little sick. Then you looked at the RAM it's eating on your server and felt a lot sick. Confluence isn't bad software — it's just heavy, expensive, and increasingly hostile to the whole "self-hosted" ethos. The good news? The r/selfhosted crowd has been fighting this war for years, and the alternatives are genuinely good.
## The Elephant in the Room: What Are You Actually Replacing?
Before you jump ship, ask yourself what you *actually* use Confluence for. If it's just a dumping ground for meeting notes and half-finished docs, you don't need a wiki. You need a folder with good search. If you're running a 50-person engineering org with complex permissions and workflows, you need something with teeth.
The thread that kicked this off had a guy running a 12-person startup on a single Hetzner CX22 (that's 2 vCPU, 4GB RAM, about €3.79/month). He was paying Atlassian more per month than his entire server cost. That math is broken.
## The Contenders, Ranked by How Much They'll Annoy You
### BookStack — The People's Champion
This is the default answer in the thread, and for good reason. It's simple, it looks clean, and it runs on a potato. I've seen it humming along on a Raspberry Pi 4 with 2GB RAM using maybe 300MB of it. Setup time? Under an hour if you're slow.
The catch: it's opinionated. You get chapters, books, and shelves. That's it. If your team needs granular page-level permissions or complex workflows, you'll fight the tool. One commenter put it perfectly: "BookStack is great until your boss asks for a 'knowledge base portal with role-based access.' Then you cry."
### Outline — The Pretty One
Outline is what happens when a wiki actually cares about design. It's fast, it's collaborative, and it feels like Notion without the corporate creep. The selfhosted version is free, but here's the kicker: it requires Redis, MinIO (or S3), and PostgreSQL. That's not a wiki, that's a microservices architecture.
I ran it on a 4GB VPS and it worked, but I was constantly fiddling with storage configs. If you're comfortable with Docker Compose and don't mind a few moving parts, it's arguably the best *experience* of the bunch. If you just want something that works, it's a trap.
### Wiki.js — The Overachiever
Wiki.js is powerful. It's got a beautiful editor, built-in auth, and it can do some genuinely clever stuff with page trees. It also has a fatal flaw: it's a Node.js app, and it shows. Memory usage creeps up, and I've seen it go from 200MB to 1.2GB for no apparent reason. The community is genuinely split on this one — half swear by it, half have horror stories about database corruption after a botched update.
## The Dark Horse: Just Use Markdown Files
Here's the take that got the most upvotes in the thread, and honestly, it's the right one for a lot of people: stop using a wiki entirely. Just use a folder of Markdown files in a Git repo. Obsidian for the desktop, a simple static site generator for publishing, and you're done.
Zero server cost. Zero maintenance. Version control built in. The downside is real though — no web-based editing for non-technical folks, and mobile access is clunky. If your team is all engineers, this is the move. If your PM needs to edit docs from their phone, you need something else.
## My Actual Recommendation
For most people, BookStack is the answer. It's the least likely to break, it's dead simple to back up (it's just PHP and MySQL), and it does 90% of what Confluence does without the bloat. I've got it running on a $5 DigitalOcean droplet alongside three other services, and I haven't touched it in months. That's the highest compliment you can pay selfhosted software.
If you need something prettier and you're comfortable with Docker, Outline is worth the pain. Just budget an afternoon for setup and a morning for the inevitable storage debugging.
Skip Wiki.js unless you have a specific reason to need it. And if you're a solo dev or a tiny team, skip all of them and just use Git.
## FAQ
**Q: Can I migrate my Confluence content to BookStack?**
A: There's no official importer, but you can export Confluence pages to HTML or Markdown and paste them in. It's manual and tedious for large wikis, but workable for under 100 pages. Budget a weekend.
**Q: How much RAM do these actually need?**
A: BookStack runs comfortably in 512MB. Outline needs at least 2GB because of Redis and the database. Wiki.js is a wildcard — anywhere from 300MB to 1.5GB depending on what it's doing.
**Q: Is selfhosting a wiki actually cheaper than Confluence?**
A: For a small team, absolutely. A $5/month VPS beats $10/user/month. But factor in your time — if you spend 10 hours a year maintaining it, that's not free. For a 50+ person org, the math gets murkier and you might be better off just paying Atlassian.
