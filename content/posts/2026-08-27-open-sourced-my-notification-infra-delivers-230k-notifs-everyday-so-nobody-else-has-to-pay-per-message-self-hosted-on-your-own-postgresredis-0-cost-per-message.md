---
title: 'Open Source Notification Systems: Why Paying Per Message Is Outdated'
date: '2026-08-27T22:00:35+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: How one project delivers 230K+ notifications daily at $0/message and why
  you should care about self-hosted infra.
---

The post started with a hook: "Self-hosted notification infra. Delivers 230K+ notifs daily. $0 per message." That’s a hell of a pitch for a sideproject. For anyone neck-deep in SaaS bills, it feels like finding out Spotify Premium is suddenly free. But does it hold up IRL? And should you even bother?
Let’s get into it.
## What This Is, and Why It’s Cool
The setup is straightforward: a rules-based notification service, entirely open source. Outputs via email, SMS, or push. Runs on Postgres and Redis, so no weird proprietary dependencies. The magic here? Costs are static. Whether you send 1 message or a million, your bill is the same—you're just paying for cloud/server overhead.
Contrast this with Twilio or OneSignal, where you’re charged per message. Scaling beyond 100K notifications per day can burn through $500+ per month. More if you’re running international. That pricing model makes sense if you're big on sending spam—I mean “engaging content”—but startups are lean. Why pay for bloat?
### What’s Under the Hood?
The repo (built in Rust, if you’re curious) promises horizontal scalability. Core infra? Two familiar names: Postgres for persistence, Redis for in-memory jobs. These tools are already the Swiss Army knives of backend infra.
The architecture here doesn’t reinvent the wheel. Queue processors fetch, transform, and fire off the appropriate notification. Error handling is where most open-source systems fall apart, but this implementation uses pre-commit hooks (at the database level) to ensure retries are clean.
It’s not perfect—thread comments mention poor documentation for Redis clustering, especially if you’re working with sentinel setups over Docker-Compose. But for local dev on bare-metal, or small-scale use? Chef’s kiss.
Setup time? The contributor claims you’ll be up in ~30 minutes if Docker’s not behaving like a toddler that swallowed a magnet. Whatever. Your first Lightning invoice probably takes longer.
## Who It’s For (and Who It’s Not)
Let’s get specific. 
If you’re running a small SaaS with under 10K notifications per day, this project is overkill. Honestly. Stick to Zapier/SendGrid and keep your devs focused on your core product. But if you’re north of 50K daily event notifications—think “every time an order ships” or “instant Slack alerts for a team of 1,000”—then this is absolutely worth a weekend of tinkering.
One commenter on the thread shared they switched from AWS SNS and shaved off $2,300/month. But someone else mentioned it became a maintenance burden. That’s the tradeoff: scale and cost efficiency vs time. Self-hosting isn’t plug and play.
Oh, and one caveat: I wouldn’t run this on ARM or with cheapo VPS providers because you’ll bottleneck disk + memory writes. Deploy it on a proper machine. Hetzner, Linode, whatever’s reliable in your region.
## Why This Matters Now
The timing matters. Every other week we’re hearing about some SaaS jacking up prices. Mailchimp went wild. Heroku bumped their dyno pricing. For notifications, recurring cost models *always* hit a wall at scale. This project flips that dynamic.
It also plays into a broader trend: pay-once tooling. I’m talking Plausible over GA, Meilisearch over Algolia. Developers are sick of SaaS lock-in and surprise invoices. No one cares if it takes a few hours to set up, as long as it buys sovereignty over cash flow down the line.
But the big question: After you’ve dodged per-message fees, what’s the cost here? Real numbers, quick math:
- Postgres on 2vCPUs and 4GB RAM = ~$35/month on Linode.
- Redis? Same deal. Call it another $35.
- Total hosting: $70/month fixed for infra that easily scales to hundreds of thousands of messages per day.
Let’s say you’re sending 230K daily. That’s 6.9M messages/month. Your cost/message = **$0.00001**. That’s 10x cheaper than Twilio, even at their "discounted bulk" pricing tiers. Wild.
## The Catch
This is not fire-and-forget software like Twilio. You need someone (presumably yourself) to monitor the setup. That includes alerts on Redis latency, Postgres locks, or queue bloat when things go sideways.
There’s also the gap between “open source” and “production-ready.” The docs are fine but not great. One user mentioned compatibility issues with other message queues like RabbitMQ. Another said cleanup scripts (for wiping read notifications) didn’t trigger properly when running over Fargate. Translation: you’ll be staring at StackOverflow on a Sunday morning.
## Should You Build or Buy?
Here’s the thing: you already know whether this excites you. Builders are gonna build. SaaS loyalists will stick to off-the-shelf tools and blame their CFO for rising costs.
I think it’s too much for pre-revenue startups. But once your app or service has traction? Time to reevaluate what you’re overpaying for. Self-hosted infra isn’t just about saving money—it’s about future-proofing your stack.
By the way, if you try this out, don't forget to comment on the repo when you hit 1M messages/day. Maintainer badges are forever.
### FAQ
#### How much RAM does this setup need?
Postgres and Redis are light on their feet compared to, say, Kafka. 2GB+ RAM per service should be fine to start, but scale as needed for high-volume queues. Test locally before you deploy anything critical.
#### Is this a good fit for mobile push notifications?
Yep. Email + APNs + Firebase all work out of the box, according to the repo details. If you’re sending device-level updates (e.g., order confirmations, alerts) this should cover almost all use cases.
#### What happens if Redis fails?
Welcome to the magic of self-hosting: Redis breaking mid-process means stuck jobs. You’ll need a retry mechanism. The defaults seem to handle common edge cases, but if this runs on shared infra, set up failovers proactively. Redis Sentinel or a managed service is worth considering.
