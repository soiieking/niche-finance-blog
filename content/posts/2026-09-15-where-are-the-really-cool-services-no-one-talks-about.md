---
title: Where Are the Hidden Gems in Self-Hosting? Cool Services Nobody Talks About
date: '2026-09-15 20:00:04+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: Tired of hearing about *the same* self-hosted apps? Here are a few under-the-radar
  services worth trying, and why they deserve more love.
---

## Cool Services You're Probably Missing

Everyone’s heard about Nextcloud, Home Assistant, and Jellyfin—and for good reason. They’re reliable and polished, so they dominate every "best self-hosted apps" list. Good for them, but there’s another layer of tools and services: the hidden gems weirdos from r/selfhosted quietly swear by. These are the experiments, the solutions to niche problems, the apps your favorite sysadmin’s basement is actually running.

Let’s talk about those.

---

### Gotify: Because You Don’t Need Pushover

Push notifications are underrated, especially when you’re juggling multiple self-hosted apps. Everyone defaults to Pushover or Telegram bots, but **Gotify** is an open-source alternative that you control entirely. It’s minimalist by design—think <10MB of RAM idle, single binary for the server, mobile apps that Just Work™. 

But here’s the kicker: it’s clean. There’s no heavy database requirement (SQLite is enough), and the REST API is so simple it's almost elegant. If you’re running something like Watchtower to monitor container updates, you could use Gotify to notify you of deployments without handing AWS SNS or Pushover $5/month.

Overkill for normal people? Maybe. But if you’re already running it on a small VPS or a Raspberry Pi, it just feels... right. 

---

### Monica CRM: Track People, Not Clients

I stumbled onto **Monica** in an r/selfhosted thread from ~2 years ago, and it’s still criminally overlooked. Officially, it’s branded as "a personal relationship manager." Unofficially, it’s for people who feel kinda guilty about forgetting birthdays and who’s allergic to what. 

The setup is solid—PHP and MySQL (or MariaDB) backend, runs well in Docker, 512MB RAM is plenty. The default UI feels like the kind of thing Google would’ve killed off in 2013—a bit clunky but functional. This is _not_ fast-moving software (releases can be sparse), but the core app works fine without much tweaking.

Pro tip: pair this with a daily cron job that grabs events from your Google Calendar and dumps them into Monica. Suddenly, you look like someone who remembers details about coworkers, friends, and ex-roommates.

---

### CodeServer + rclone: Poor Man’s Dev Box

If you’re spending $20/month on VS Code Spaces or GitHub Codespaces, you’re doing it wrong. **CodeServer** is a self-hosted VS Code instance, and when you bolt on **rclone**, it becomes surprisingly powerful for backend-only dev work.

The hack: use rclone to mount your cloud storage (Google Drive, Backblaze, Dropbox) as a pseudo-filesystem, and CodeServer to edit the files directly. Throw in Tailscale or Zerotier for remote access, and there you go—a free lightweight alternative to managed dev environments.

Main downside? CodeServer eats at least 150MB of RAM idle, which can be gross if you’re on a tiny VPS. But for bigger machines, this combo punches well above its weight.

---

### Plausible: Analytics Without the Guilt

*“Just use Google Analytics.”* Yeah, no thanks. If you’re sick of scripts bloating your website and the endless creep of invasive corporate widgets, you owe yourself a look at **Plausible**.

It ticks all the boxes: GDPR-friendly, open source, about 60ms of average loading time impact (vs GA’s 500ms death march). You can self-host it on ~1GB of RAM though CPU load spikes if you’re handling real traffic (e.g., 50K page views/day). 

In an r/selfhosted thread from last December, someone compared it to Matomo, and IMO, they’re wrong. Matomo is **everything-but-the-kitchen-sink** analytics; Plausible thrives on simplicity. Data is sparse, but that’s the point. If you want actionable insights like where users click without sifting for hours, Plausible is your new best friend.

---

## Honorable Mentions (If You’re Feeling Brave)

- **Kavita**: Think of it as self-hosted Kindle Unlimited for PDFs, EPUBs, and manga. Less bloated than Calibre-web, more polished than Ubooquity. Just don’t expect massive community support yet—it’s still finding footing.
  
- **PrivateBin**: Pastebin clone that lets you encrypt stuff client-side. I run this for quick collaboration outside Slack/MS Teams hell.

- **Firefly III**: The self-hosted finance tracker that nobody will shut up about—if you’re a budgeting nerd, try it. Pro tip: works well with Plaid API for bank imports (though setup is non-trivial).

---

### Why Aren’t These More Popular? 

Two reasons: niche appeal and community inertia. Most people don’t need a private notification server or care about minimal web analytics. And defaulting to "big-name" apps feels safer. But part of the fun of self-hosting is scratching that itch for something *better suited to you*. 

These projects, even the weird ones, have communities you can poke into on GitHub or Discord. And they let you experiment without a SaaS middleman breathing down your neck.

So dive in. Break stuff, fix it later. That’s how you find the cool toys.
