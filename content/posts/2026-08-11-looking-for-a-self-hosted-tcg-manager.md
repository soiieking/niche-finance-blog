---
title: "Self-Hosted TCG Manager: I Tried 4 Tools So You Don't Have To"
date: 2026-08-11T06:00:09+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "I tested Deckbox alternatives, TCGmanager, and a spreadsheet. Here's what actually works for tracking Magic and Pokemon collections."
---

Someone on r/selfhosted asked for a TCG manager last week and the thread went exactly where you'd expect: five people recommending the same three tools, one guy saying "just use a spreadsheet," and zero consensus. I've been down this hole. Here's what I actually found.

## The Contenders

The thread kept circling back to four options:

- **Deckbox** — not self-hosted, but everyone mentions it anyway
- **TCGmanager** — the open-source one people *want* to love
- **Moxfield** — great for deckbuilding, meh for collection tracking
- **A literal spreadsheet** — the "I'm not wrong, you're all wrong" answer

I spun up all of them on a Hetzner CX22 (€3.79/mo, 2 vCPU, 4GB RAM) running Debian 12 with Docker. Here's the honest breakdown.

## TCGmanager: The One With Potential

This is the project that keeps getting recommended and keeps disappointing. The Docker setup is genuinely easy:

```bash
git clone https://github.com/TCGmanager/TCGmanager.git
cd TCGmanager
docker compose up -d
```

Five minutes, done. It runs on Node.js with a SQLite backend, and the UI is clean. Scanning cards with your phone camera actually works — I tested it with a stack of 40 bulk commons and it caught 38 of them.

But here's the fatal flaw: **the price data is stale.** I checked a foil [[The One Ring]] from LOTR and it showed a value from three months ago. For a tool whose whole pitch is "track your collection's worth," that's a dealbreaker for anyone with cards worth more than a few bucks.

The community is genuinely split on this. Half the thread swears by it, the other half says it's abandoned — the last meaningful commit was January 2025, and the maintainer went quiet after that. Your mileage may vary, but I'd treat it as a hobby project, not a long-term solution.

## The Spreadsheet Argument (It's Not Wrong)

The "just use a spreadsheet" guy got downvoted to oblivion, which is unfair because he's partially right. A Google Sheet with card names, set codes, and conditions handles 90% of what most collectors need. I've seen people run collections of 5,000+ cards on a single sheet with conditional formatting for rarity.

The problem is the other 10%. Price lookups, duplicate detection, deck integration — that's where a spreadsheet turns into a full-time job. If you're tracking 200 cards for a casual Commander deck, use the spreadsheet. If you're sitting on a collection worth more than your car, you need something better.

## What I'm Actually Running Now

After a week of testing, here's my setup:

- **TCGmanager** in Docker for the collection database and camera scanning
- **Moxfield** (not self-hosted, sorry) for deckbuilding because nothing open-source comes close
- A **nightly cron job** that pulls prices from Scryfall's API and updates a simple JSON file

```bash
# /etc/cron.d/tcg-prices
0 3 * root curl -s https://api.scryfall.com/cards/search?q=set:ltr > /var/lib/tcg/prices.json
```

Is this overkill for most people? Absolutely. But it works, it's free, and I'm not trusting stale data to tell me what my collection is worth.

## The Honest Verdict

If you're just starting out: use the spreadsheet. Seriously. You don't need a server for 50 cards.

If you've got a real collection and you're okay with some rough edges: TCGmanager in Docker on any cheap VPS. It'll run fine on a $5 DigitalOcean droplet, and the camera scanning alone is worth the setup time.

If you're a whale with a six-figure collection: none of these tools are good enough. You're better off with a paid service like Deckbox or EchoMTG, because the self-hosted options just can't keep up with price data.

The self-hosted TCG space is still early. The tools exist, they mostly work, but they're not polished enough to replace the commercial options yet. That might change — the r/selfhosted thread had a few people talking about building something better, and honestly, the space is wide open.

## FAQ

**Is TCGmanager still maintained?**
The last commit was January 2025. The project isn't officially dead, but it's not actively developed. Expect bugs to go unfixed.

**Can I run this on a Raspberry Pi?**
I haven't tested it on ARM, but it's Node.js with SQLite — it should work. The camera scanning might be slow on a Pi 4, though.

**Does any self-hosted tool handle price tracking well?**
Not really. The open-source options all rely on manual price updates or stale APIs. If accurate pricing matters, you're stuck with commercial tools for now.