---
title: My Side Project Became Our Full-Time Business. It Started With My Info Getting
  Sold
date: '2026-08-14T02:00:41+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding My Side Project Became Our Full-Time Business. It Started With
  My Info Getting Sold.
---

to a Dozen Lenders.
The origin story is almost too on-the-nose. A guy on r/sideproject posts that his entire business started because his personal data got sold to a dozen mortgage lenders. He was drowning in spam calls, built a tool to block them, and accidentally created something people would pay for.
I love this story because it's not a "I quit my job to chase my passion" fairy tale. It's revenge. Pure, petty, profitable revenge.
## The accidental product
The original post was light on specifics, but the thread filled in the gaps. The founder built a call-filtering app for Android — not iOS, because Apple's CallKit sandbox makes this kind of thing a nightmare. He was tired of the "we've pre-approved you for a HELOC" calls at 7 PM.
The first version was a weekend hack. A simple regex filter, a blocklist, and a notification log. He shared it on Reddit, got 200 upvotes, and thought that was the end of it.
Then the emails started. "Can you make this work for my mom?" "Does this work on Samsung?" "Will you take $5 for the APK?"
He wasn't trying to build a business. He was trying to get his dinner uninterrupted.
## The pivot that wasn't
Here's where most side projects die. The founder had a choice: keep it free and move on, or start charging. He chose to charge — $2.99 one-time, no subscription. That was the smartest dumb decision he could have made.
The thread's comments are genuinely split on this. Some argue a subscription would've been better for recurring revenue. Others point out that a one-time fee lowered the barrier to entry and got him early adopters who then became evangelists.
I side with the one-time fee. For a utility app, subscriptions feel like a hostage negotiation. You're not Netflix. You're a spam blocker. Charge once, build trust, then figure out the upsell later.
## The numbers that matter
The founder eventually shared some rough figures in a follow-up comment. I'm paraphrasing because the thread's been deleted, but the gist was:
- **$40k MRR** by month 14
- **~13,000 paying users** at an average of $3 per purchase
- **Zero paid marketing** — all organic via Reddit, Product Hunt, and word of mouth
- **One server** running the whole thing on Hetzner's cheapest CX22 instance ($4.50/month)
That last point is the one that gets me. He's running a business that does half a million a year on a single $4.50 VPS. No Kubernetes. No microservices. No "cloud-native" nonsense. Just a Go binary, a SQLite database, and a cron job.
This is the opposite of what most tutorials tell you. The r/sideproject community has a running joke about people who spin up a DigitalOcean droplet, add Docker Compose, and then spend three weeks "architecting" a solution for a problem that has 12 users. This guy just shipped.
## The ugly side
It wasn't all smooth sailing. The thread had a few honest confessions:
- **Google Play rejected his app twice** for "deceptive behavior" because the call-log permissions looked sketchy. He had to write a 2,000-word appeal explaining exactly how the data was used locally.
- **He got doxxed** by angry telemarketer who found his personal email and signed him up for every newsletter imaginable. The irony was not lost on him.
- **He almost quit at month 6** when a competitor launched a free version with VC backing. The community talked him out of it by pointing out that the competitor's app was harvesting contact lists for ad targeting.
That last point is worth dwelling on. The free competitor was doing what free apps do — monetizing user data. Our founder's entire business was built on the premise that your data shouldn't be sold. He couldn't compete on price, so he competed on trust. That's a real moat, but it's a slow one to build.
## What I'd do differently
If I were starting this today, I'd skip the Android-first approach. The Play Store is a swamp of copycats and permission-scare stories. I'd build a simple web app that works with Twilio's number lookup API and let people forward their spam calls to a honeypot number. No app store review, no permission hell, just a $20/month SaaS.
But that's me. The founder's approach worked because it was personal. He was the target user. He felt the pain every single day. That's something you can't fake.
## The takeaway
This business isn't a unicorn. It's not even a startup in the traditional sense. It's a guy who got annoyed, built a fix, and charged money for it. The entire r/sideproject thread is a masterclass in why "scratch your own itch" still works in 2026.
The community is genuinely split on whether this is replicable. Some say it's survivorship bias — thousands of people build spam blockers, one gets traction. Others say the lesson is simpler: find a problem that makes you angry, and solve it for yourself first.
I'm in the second camp. The anger is the fuel. The product is just the vehicle.
Your mileage may vary. But if you're reading this and you have a half-finished side project sitting in a GitHub repo, ask yourself one question: does it make you angry enough to finish?
