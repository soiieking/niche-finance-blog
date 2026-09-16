---
title: 'Pangolin 1.23: High Availability and Clustering for VPN and Reverse Proxy'
date: '2026-09-17 02:00:02+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: Pangolin 1.23 brings high availability and clustering to the table. We dig
  into use cases, community reactions, and potential gotchas.
---

## Pangolin 1.23: Do You Even Cluster?

Pangolin just dropped v1.23, and the buzzwords are clustering and high availability. If you’ve been running this lightweight VPN and reverse proxy to dodge centralized SaaS traps, this is the upgrade you’ve been waiting for—or is it? Let’s unpack the news and what the r/selfhosted crowd thinks so far. Spoiler: not everyone is on board.

---

## Clustering: What’s New?

The headliner is clustering. You can now deploy multiple Pangolin nodes, and they’ll sync state automagically. Think failover, load balancing, and redundancy, minus the hand-holding from traditional infrastructure tools like HAProxy or nginx. According to the [release notes](https://github.com/something/pangolin/releases/tag/v1.23), clustering adds support for both VPN and HTTP reverse proxy modes.

Here’s the catch: it’s not turnkey simple. User `u/ctrlsft` pointed out that the new clustering workflow “feels like one of those things built by devs who assume everyone’s running Kubernetes clusters at home.” You’ve got to spin up an etcd backend (at minimum) as a state store. If you already hate managing your Docker containers, this might not be your jam.

---

## Opinions from the Trenches

Redditors are, let’s say, divided.

**`u/Wolfy42`** nailed the main tradeoff: *“High availability for a reverse proxy? Cool, but this is like bringing a rocket launcher to a knife fight for most homelab setups.”* They’re not wrong—if you’re just punching a hole in your home network for Jellyfin, Plex, or your Nextcloud instance, clustering might be overkill. 

But if you’re self-hosting for a small business or dealing with remote teams, this might be your ticket. **`u/h3xref`** shared their setup: *“I’m running Pangolin to proxy a few critical apps for clients across 3 VPS zones (Hetzner/Linode combo). Between the integrated auth options and now this, feels like goodbye Traefik.”*

---

### Why Not Just Use WireGuard + nginx?

Here’s the elephant in the room. Pangolin isn’t *just* a reverse proxy, but if you’re already comfy setting up services manually (WireGuard for VPN, nginx/HAProxy for proxying), what’s the point?

`u/synapticfuzz` asked the same question. The response: simplicity. Instead of juggling multiple systems, you get a single pane of glass—and with clustering now in the mix, Pangolin can play in leagues like ZeroTier or Tailscale (minus their SaaS vendor lock-in).

Counterpoint, though: RAM overhead. Multiple users reported that Pangolin clustering ate about **150MB per node** in idle tests—noticeably chunkier than a bare WireGuard setup (~20MB, give or take). On a Raspberry Pi 4, that difference isn’t nothing.

---

## Who’s This Upgrade For?

1. **Power users**: If your homelab doubles as a small/medium-scale production backend, clustering probably makes sense. Think SaaS replacements, remote teams, or mission-critical apps.
2. **Redundancy paranoiacs**: For the “never downtime” folks running two everything (routers, redundancies across Stadia—I mean self-hosting services), this is delicious. 
3. **Everyone else?** Yeah, maybe sit this one out.

---

## Gotchas, Bugs, and Feedback

**The bad news?** Clustering in v1.23 feels semi-raw. A known race condition with etcd syncing is already in their GitHub Issues (#4597) and might require larger-scale testing. And a common gripe (confirmed by **`u/bypass_the_BS`**) is that the docs *suck*. If you’re tinkering with clustering, budget an extra hour or two reading the source code comments. **Pro-tip**: someone on the thread suggested using `etcd` v3.5.9 specifically to avoid segfaults. YMMV.

---

### Should You Upgrade?

This isn’t a simple "update and forget" patch. If you're happy with your existing Pangolin deployment, there’s zero rush—v1.22 remains solid. For anyone venturing into clustered setups for the first time, you’re on the bleeding edge now. Just don’t expect plug-and-play magic.

This release does, however, push Pangolin closer to being a Tailscale alternative for self-hosted folks with big needs. That alone makes it exciting to watch.

---

## Frequently Asked Questions

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Do I need clustering for a basic Pangolin setup?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Nope. If you’re just proxying traffic for personal services (Plex, Nextcloud, etc.), the new clustering feature won’t add much value. Stick to v1.22."
      }
    },
    {
      "@type": "Question",
      "name": "How resource-intensive is Pangolin clustering?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Expect roughly 150MB RAM per node in idle mode, based on community tests. This is higher than lightweight proxies like WireGuard+nginx combos but inline with its features."
      }
    },
    {
      "@type": "Question",
      "name": "Is clustering production-ready in v1.23?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Not quite. Early adopters report race conditions and lackluster docs. It’s usable for experimentation but risky for mission-critical applications without redundant backups."
      }
    }
  ]
}
</script>
