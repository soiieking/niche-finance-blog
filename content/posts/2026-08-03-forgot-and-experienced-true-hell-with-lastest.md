---
title: "The True Hell of Docker :latest and How to Actually Pin Your Stack"
date: 2026-08-03T18:45:10+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Why using :latest in your docker-compose files will eventually burn your stack down, and the exact ways to prevent it."
---

We’ve all done it. You spin up a quick Docker container for some tiny utility, you don't care about the version, so you just grab `:latest`. Fast forward six months, you run a routine `docker compose pull` on your $14 Hetzner VPS, restart the stack, and everything violently catches fire.

Someone on r/selfhosted posted a glorious vibe-check earlier this week about exactly this. They had a perfectly stable setup, yanked the latest image, and the maintainer had pushed a major version bump that required a completely different database schema. Boom. Data gone, container crash-looping, 3 AM panic. 

`:latest` is not a feature. It’s a loaded gun pointed directly at your own foot.

## Why :latest is a trap

The tag "latest" doesn't actually mean the most stable release. It just means the most recent image the maintainer pushed to the registry. Sometimes that’s a minor patch. Sometimes it’s a complete architecture rewrite that expects a totally different volume mapping.

When you automate your updates via Watchtower or Ouroboros while using `:latest`, you are volunteering for continuous integration on your personal production environment. It's lateral idiocy. I love Watchtower for keeping my CVEs patched on my local dev box, but it has absolutely zero business running on a server you actually rely on.

## The right way to pin

You have three viable tiers for tagging images in your compose file, and only one of them is actually correct for a stable server.

### 1. The Major Tag (e.g., `nginx:1`)

This pins the major version number. You get security patches and minor feature updates automatically, but you theoretically avoid breaking changes. 

But here's the gotcha: maintainers are human. A `1.25` to `1.26` update can still ship with a massive memory leak that consumes your 2GB VPS if the maintainer didn't catch it. I haven't tested this on ARM specifically to see if the memory bleed is worse, but on amd64 it’s burned me twice. You aren't entirely safe just because you aren't tracking the patch number.

### 2. The Patch Tag (e.g., `nginx:1.25.3`)

This is exactly what you should be doing. Pin the exact patch version. 

If you pull this image, you will get a perfect carbon copy of what the maintainer published on that specific day. It won't change. It won't break. You can go on vacation and the container will still be idling when you get back. The trade-off? You are responsible for manually bumping the version string when a critical CVE drops. I spend about 15 minutes a month checking my primary stack for updates. It's a tiny price to pay for literally never waking up to a broken API.

### 3. The Digest Pin (e.g., `nginx:1.25.3@sha256:...`)

This is the ultimate paranoid mode. Even if a maintainer gets hacked and retroactively pushes a malicious binary to the `1.25.3` tag, your server will reject it because the hash won't match. This is overkill for most people running Plex and Gitea on a home server, but if you're璤running infrastructure for friends or a small business, you absolutely need this. Coworker script-kiddie paranoia pays off against supply chain attacks. 

## Docker vs Podman for update management

How you manage these pinned updates matters. The community is genuinely split on Docker vs Podman.

I prefer standard Docker because Portainer v2.19+ gives me a dead-simple UI to show which containers have pending image updates. I know Portainer is generally frowned upon by the r/selfhosted greybeards who think real men only use raw bash, but it's a perfectly valid tool for visualizing your stack. 

Podman is great for daemonless setups, but checking for updates on a headless Podman machine usually means shelling out for something like Podman Auto-Update, which muddies the simplicity. Your mileage may vary depending on your Linux-fu.

## The cleanup nightmare

Even if you pin your tags perfectly, Docker leaves digital trash everywhere. Every time you pull a new image, the old dangling layers stay on your disk. You will eventually fill up your SSD and corrupt your volumes.

Do not run `docker system prune -a` if you have stopped containers you care about. It will nuke them. Use `docker image prune` instead. That specifically targets untagged images and keeps your active containers intact.

Stop using `:latest`. Pin to an exact version string. Verify the digest if you need to. You can automate your infrastructure, or you can have a stable life, but with `:latest` you rarely get both.