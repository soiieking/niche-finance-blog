---
title: 'The 2026 Selfhosted Stack: What r/selfhosted Actually Runs Now'
date: '2026-08-14T12:00:43+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding The 2026 Selfhosted Stack: What r/selfhosted Actually Runs Now.'
---

The weekly megathread dropped Tuesday and it's a mess in the best way. 214 comments, three deleted threads about Plex, and one guy asking if he can host Nextcloud on a Raspberry Pi Zero. Again.
Here's what actually matters from this week's dump.
## The Hetzner Migration Is Real
Every third comment mentions moving off DigitalOcean. Not because DO is bad — because Hetzner's CX22 (2 vCPU, 4GB RAM) costs €3.79/month. That's less than a coffee in Berlin. One user, u/backup_bandit, posted his migration notes: cut his monthly bill from $24 to €11 by moving three containers over. Setup took him an afternoon.
The catch? Hetzner's support is ticket-only. No live chat. If your box dies at 2am, you're waiting. DO's $6 droplet is still fine for people who value their sleep.
## Podman Finally Won
The "Docker vs Podman" argument has been running for years. This week it ended. Multiple users reported running Podman 5.x in production without root, and the daemon-less architecture is winning converts. One commenter, u/container_hermit, said he switched because Docker Desktop's licensing fees hit his small consultancy for $300/year. Podman is free.
The killer feature nobody talks about: systemd integration. Podman generates systemd unit files automatically, so your containers survive reboots without a separate orchestration layer. Docker needs compose files and restart policies. It's not a huge deal for a home lab, but it matters when you're running 14 services.
## AI Gateways Are the New Pihole
The biggest surprise: everyone's hosting LLM proxies. Not running models locally — proxying OpenAI, Anthropic, and local Ollama instances behind a single API endpoint. Tools like LiteLLM and OpenWebUI are getting more mentions than Traefik.
u/llm_hoarder posted his setup: a $5 VPS running LiteLLM with 12 different model providers, giving his homelab a unified API key. He claims it cut his token spend by 40% by automatically routing simple queries to cheaper models. I haven't tested this on ARM, but the logic checks out.
The community is genuinely split on whether this is overkill. If you're just running one chatbot for your family, a proxy is pointless. If you're building anything serious, it's becoming mandatory.
## The Storage Wars Continue
Someone asked about NAS drives and the thread exploded. The consensus: used enterprise drives from eBay are the move. u/data_hoarder_2026 bought four 14TB SAS drives for $180 total, running them in a used Dell R730xd he picked up for $350. Total storage cost: under $0.01/GB.
The counter-argument came from u/quiet_reliability: "I've had two used drives fail in six months. My time is worth more than the savings." Fair point. Your mileage may vary, but the math is hard to ignore when new 14TB drives run $250 each.
## What I'm Actually Running Now
After reading this week's thread, I moved my own stack. Here's the final setup:
- **Hetzner CX22** — $3.79/month for the main VPS
- **Podman 5.3** — running 8 containers, no root
- **LiteLLM** — proxying 6 AI providers
- **Syncthing** — replacing Nextcloud for file sync (it's lighter, 80MB RAM vs 500MB+)
- **Vaultwarden** — because Bitwarden's hosted tier is $10/year and this is free
Total monthly cost: $4.12. Total setup time: about 4 hours spread over a weekend.
## The Bottom Line
The megathread's real signal: selfhosting has shifted from "look what I built" to "look how cheaply I run this." People aren't showing off homelab racks anymore. They're showing off monthly bills under $10.
The tools changed too. Docker's dominance is cracking, AI proxies are the new essential service, and used enterprise hardware is the only way to store data without going broke.
If you're still running everything on Docker with a $50/month cloud bill, this week's thread is your wake-up call.
## FAQ
**Is Hetzner reliable enough for production?**
Generally yes, but their support is ticket-only. If you need 24/7 human assistance, pay more for DigitalOcean or Linode. For homelabs and side projects, Hetzner's price-to-performance is unmatched.
**Do I need an AI gateway if I just run one chatbot?**
No. A direct API call works fine. Gateways like LiteLLM become valuable when you're juggling multiple providers, need usage tracking, or want to route requests to cheaper models automatically.
**Are used enterprise drives worth the risk?**
Financially yes — you can get 10x the storage for the same price. But have backups. The drives are typically pulled from data centers with high usage hours, so failure rates are real. If your data is irreplaceable, buy new.
