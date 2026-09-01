---
title: I Tried to Teach My Kid the Multiplication Table and Accidentally Built a Game
date: '2026-08-12T12:00:15+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding I Tried to Teach My Kid the Multiplication Table and Accidentally
  Built a Game.
---

The original r/sideproject post was classic: "Tried to teach my son the multiplication table, ended up building a game." The comments were half "this is the way" and half "monetize it before he outgrows it." I've been there. My own kid memorized the periodic table through a janky quiz app I threw together in a weekend. It crashed constantly. He loved it anyway.
That's the secret. Kids don't care about your architecture. They care about the dopamine hit when the dragon breathes fire after they answer 7×8 correctly.
## The Accidental Product
The OP didn't set out to build a business. He set out to stop the nightly screaming match over flashcards. The game he built was simple: answer multiplication problems to unlock levels, earn stars, beat a timer. Nothing revolutionary. But it worked because it solved a *specific* problem for *one* person.
That's the r/sideproject sweet spot. The best projects come from scratching your own itch, not from market research reports. I've seen too many people build "Uber for dog walking" when they should've built "the thing that stops my kid from crying at 7 PM."
The thread had a comment that nailed it: "Your son is your first user, and he's the most honest one you'll ever have." Kids don't give polite feedback. They either play it or they don't. If they don't, you've got nothing.
## The Numbers That Matter
Here's where it gets real. The OP mentioned his game hit around 40,000 users in the first few months. That sounds impressive, but let's break it down:
- **Hosting costs**: He was on a $5/month Hetzner VPS. That's the right call. DigitalOcean would've cost $6 for the same specs, and you're paying for the brand name.
- **Time to build**: He said about 40 hours total. That's two solid weekends. Most side projects die because people spend 40 hours on the *setup* and never ship.
- **Revenue**: This is where the thread got spicy. He had ads and a $2.99 one-time unlock. The community was split — some said subscriptions are the only way, others said one-time purchases build goodwill with parents.
My take? For a kids' educational app, subscriptions feel predatory. Parents are already paying for school supplies, tutoring, and therapy. A $2.99 lifetime unlock is honest. You won't get rich, but you'll sleep at night.
## The Technical Reality Check
The OP built this with plain JavaScript and a simple backend. No React, no Next.js, no Docker. Just HTML, CSS, and a few API endpoints. This is overkill for most people, but it's also the right call for a project this size.
I've seen side projects die because the developer spent three weeks setting up Kubernetes clusters for a game that serves 100 requests a day. Docker vs Podman? Who cares. You're not running a data center. You're running a multiplication game.
The one thing I'd push back on: he used a SQLite database. That's fine for now, but if he hits 100k users, he'll need to migrate to Postgres. The community is genuinely split on this — some say SQLite scales fine, others say you're an idiot if you don't start with Postgres. My experience? SQLite will handle 40k users without breaking a sweat. Migrate when it hurts, not before.
## Why This Matters Now
Here's the thing nobody in the thread said explicitly: educational apps are a graveyard. The App Store is full of dead multiplication games with 3-star ratings and last-updated dates from 2019. The OP's game succeeded because it was *personal*. It had his son's name in the code comments. It had levels designed around what his kid struggled with.
That's the indie hacker advantage. You can't out-Google Google, but you can out-love them. A parent who built a game for their kid will iterate faster than a team of 50 engineers at a edtech unicorn. You'll respond to feedback in hours, not quarters.
The thread had one comment that made me laugh: "Your son is going to be 25 and still telling people his dad built a game for him." That's the real ROI. The 40k users are nice. The revenue is nice. But the fact that your kid thinks you're a wizard? That's priceless.
## The Hard Truth
Not every side project needs to be a business. The OP's game might plateau at 40k users and never make more than $200 a month. That's fine. It's still a win.
But if you want to push further, the next step is clear: build a version for teachers. Classrooms are where the real volume is. One teacher who uses your game in class means 30 kids who take it home. That's the growth loop nobody in the thread mentioned.
The community is genuinely split on whether to go B2B or stay consumer. I haven't tested the teacher route myself, but I've seen it work for other educational tools. Your mileage may vary.
## FAQ
**Q: How long does it actually take to build a simple educational game?**
A: If you know basic JavaScript and HTML, you can have a playable prototype in 10-15 hours. The OP spent about 40 hours total, including polish and deployment. The key is to ship something ugly that works, then iterate based on your kid's feedback.
**Q: Is it worth monetizing a kids' educational app?**
A: It depends on your goals. A $2.99 one-time unlock on 40k users is roughly $120k gross, minus Apple/Google's 30% cut and hosting costs. That's real money. But if you're building for your kid first, monetization can wait. The OP's approach — ads plus a cheap unlock — is a reasonable middle ground.
**Q: What's the best stack for a side project like this?**
A: Plain JavaScript, a simple backend (Node or Python), and SQLite. Skip the framework wars. If you outgrow it, migrate to Postgres and add a CDN. The OP's setup on a $5 Hetzner VPS is more than enough for tens of thousands of users. Don't over-engineer a game about multiplication tables.
