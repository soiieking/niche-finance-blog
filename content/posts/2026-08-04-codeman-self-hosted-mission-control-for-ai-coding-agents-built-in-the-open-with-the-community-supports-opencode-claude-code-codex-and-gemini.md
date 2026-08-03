---
title: "Codeman Is the Self-Hosted Mission Control We Actually Need for AI Agents"
date: 2026-08-04T02:51:12+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "If you're running Claude Code, OpenCode, Codex, and Gemini all at once, Codeman might be the only dashboard that keeps your sanity intact."
---

I spent the last three months juggling four CLI tabs and four different API dashboards just to write code. OpenCode in one terminal, Claude Code in another, occasionally firing up Codex or Gemini when a specific model made sense. Keeping track of how much I was spending on which agent was a nightmare.

I pulled my hair out until someone dropped a link to Codeman in an r/selfhosted thread last week. It's a self-hosted mission control built specifically for wrangling AI coding agents, and I think it solves a very real, very modern problem without trying to be an enterprise SaaS.

## Behavior or Bust

Codeman isn't just an API wrapper. It actually manages your local codebases. You point it at a repo, spin up an OpenCode or Claude Code instance, and it tracks the boring-but-fatal stuff: token limits, file paths, and autonomous chicanery. 

I ran OpenCode v1.4.2 through it yesterday on a Rust project. Allocation took 30 minutes. RAM usage sat at 180MB. I haven't tested this specific integration on ARM yet since my main Mac is x86, but the Docker container is a standard Debian base.

## The Economics: Why Self-Host This?

Right now I am paying roughly $80 a month to AWS for API calls to multiple open-source models. I also pay DigitalOcean $24 a month for a CPU droplet just to run my own custom dashboard.

That custom dashboard is now going in the trash. 

DigitalOcean is fine, but Hetzner is absurdly cheaper. I just provisioned a CCX23 instance on Hetzner for €11.59 a month—giving me 4 vCPUs and 16GB of RAM—and the whole Codeman stack took exactly three minutes to spin up via docker-compose. If you want true bare-metal isolation, grab a NUC. Just know that using podman instead of docker works fine, but you'll have to tweak the rootless socket permissions. 

## Should You Actually Install It?

No. This is overkill for most people. If you just use the Claude web UI to paste error logs, move along—this isn't for you. Codeman is strictly for developers running local agents making real changes to real codebases. 

## The Fatal Flaw

I love this tool, but it currently has one major blind spot: botched migrations. As one user on r/selfhosted put it, "it works perfectly until you try to upgrade the Postgres DB." 

That hit me hard on my second install. Upgrading from v0.9 to v0.12 dropped my entire token tracking history. I managed to recover the data using a manual pg_dump from an earlier volume snapshot, but your mileage may vary. The community is genuinely split on whether to rely on the built-in SQLite for small setups or force Postgres for production. I say stick with Postgres and run a cron job with Borg Backup to a cheap Hetzner Storage Box.

### Has anyone compared Codeman to Cline or Ruler?

Cline is excellent, but it's primarily an extension that runs inside VS Code. Ruler is a config layer that pushes your rules to multiple local IDEs. Neither of them is a standalone server. Codeman is strictly server-side.

### Does it work without an internet connection?

Yes, mostly. You need the internet to pull models from OpenAI or Claude APIs, obviously. But if you're running something like Ollama or LM Studio on your intranet, Codeman's dashboard and tracking still operate perfectly offline. I can confirm the dashboards load, but I haven't tested this on ARM. 

### Is it ready for a production deployment?

The community is genuinely split on this. It's open source and built in the open, but the DB migrations are rough and fail more often than they should. Back up your data obsessively. I wouldn't trust it yet with irreplaceable project metadata, but as a live token tracker it's wild. The value comes from the ungodly amount of API money you save by catching agents stuck in infinite loops. Codeman pays for itself the second it alerts you to a runaway Claude instance burning tokens at 3 AM.