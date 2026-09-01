---
title: 'How I Went From 1 to 4 Customers in Six Days: Lessons and Next Steps'
date: '2026-09-01T04:00:11+08:00'
draft: false
tags:
- indie-hacker
- entrepreneurship
- side-project
summary: Four customers in six days sounds nice, but here’s the play-by-play of what
  actually mattered—and what probably didn’t.
---

## It’s Not Viral Growth, But It’s Growth
Six days ago, "*I signed my first customer.*" Three days later, I signed my second. Yesterday, another two decided to give me their credit card details. Now I’m sitting here trying not to overthink it. Four customers isn’t enough to brag—but it’s enough to learn from. 
Here’s what worked (and what didn’t).
## ### Step 1: Your First Customer = Proof of Life
Before the first customer, your app is Schrödinger’s cat. It’s *maybe* a business, *maybe* a failed hobby. The moment a real person pays you money, it’s real. Suddenly, you notice the bugs, edge cases, and missing polish, because someone *other than your family* is using it.
Take that energy. Channel it into obsessing over their experience.
**What I did next:**  
- **Talked to them daily.** I set up a private Slack channel for onboarding, questions, random feedback—whatever. Yes, it feels clingy. That’s fine at this stage. One of my customers flagged a broken flow I didn’t hit during QA: new users couldn’t log in again after signing up. (Classic rookie mistake, right?) That one fix made them go from “meh” to “OK, this thing’s solid.”
- **Gave them outs.** Weird advice, but hear me out: at least once, I told each user, “If this isn’t useful right now, I totally understand.” None of them left. But that statement made me trust *their* trust in the product more, because I wasn’t strong-arming anyone to stick around.
Your first goal isn’t scale, revenue, or fancy features. It’s punching through the wall of self-doubt. *One customer*, even a $10/month SaaS deal, does that.
### Step 2: Customers 2 and 3 Happened Because of Small Wins
Here’s the catch: you can’t just sit pretty after signing that first customer. It’s tempting to dream about customer #100 while avoiding the dirty “sales” work in front of you. But the step from 1 to 2 matters *way more* than the step from 99 to 100.
I found customers 2 and 3 by mining the same places I’d already been hanging out. *Existing networks are gold.* For me, r/some_small_subreddits and Twitter DMs worked best.
**Who these small wins came from:**
- **Customer 2:** Saw my original r/sideproject post and sent me a DM, saying, “This sounds like what we’re looking for.” 
- **Customer 3:** A friend-of-a-friend who’d been stuck with a similar problem for two months. Literally took a 10-minute Loom walkthrough with screen share to close.
Takeaway: Posting publicly builds long-term momentum. But warm, private conversations are what actually get the next customer.
### Step 3: Scaling from DMs to Landing Pages
Four customers isn’t success, but it’s a start. The obvious next step? Making onboarding *less manual*. When signing up my first few customers, I wrote way too many Slack messages like, “Let me manually invite you and set up your account.”
Now I’m making a public landing page and automating some of the repetitive steps. Even a simple system like this can save time:
**What I’m building, technically:**
- Framework: Huginn for task automation (because Zapier is overkill at my current scale).
- Backend: Simple Flask app running on Django (Postgres DB for now).
- Hosting: Hetzner cloud instances (€4.90/month plan—already loving it).
I’m cheating slightly. My landing page isn’t going to be crazy-polished. Think basic Tailwind design plus a Stripe button. Simple, fast, not ugly. Eventually, I’ll look at Webflow if I get past ten customers.
## Lessons From 6 Crazy Days
This whole past week taught me one big, embarrassing lesson: I was overthinking this *so much*. I used to think you needed perfect UX, five integrations, and 100 fancy features *before* anyone would pay you. Nope.
Here’s the breakdown:  
- **Sales are messy at first.** Your first few customers don’t care if your UI looks like it was built at 2 a.m. They *do* care if you solve their problem and are responsive.  
- **Free tools are enough until you hit 10 customers.** Airtable, Huginn, and a €5 Hetzner instance will carry you *way* further than AWS or fancy SaaS stacks.  
- **Every repeatable task becomes tomorrow’s bottleneck.** I had a long email thread explaining the product three times over to different buyers. I now wish I wrote that into a simple FAQ sooner.
Tweak as you go. You’re never done.
## FAQ
### How did you price your product for early users?
I went with $12/month, flat rate. Not free, because I want people who *actually value this*. But also not expensive enough to scare off first adopters. Pricing V1 assumes light usage—I’ll move to tiered plans once I add features worth charging more for.
### Why Hetzner over AWS/DigitalOcean?
Cheap simplicity. Hetzner’s UI doesn’t fight me, and the pricing is crystal-clear. Plus, a single €5 VPS doesn’t feel like renting a Ferrari to deliver pizza. AWS makes sense when you're at Figma scale; for four customers, use the Honda Civic of hosting.
### Any tools for scaling customer conversations?
Eventually move off Slack for support. A self-serve knowledge base (like Notion pages or a ReadMe.io setup) reduces repetitive DMs. Something like Crisp.chat works well for responding to on-site users at this size—but no need for Intercom price bloat yet.
