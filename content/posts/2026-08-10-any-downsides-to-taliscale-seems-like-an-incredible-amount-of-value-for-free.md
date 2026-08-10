---
title: "Tailscale Downsides: What r/selfhosted Doesn't Tell You Before You Self-Host"
date: 2026-08-10T18:00:06+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Tailscale is free and magical, but it has real downsides. Here's what the r/selfhosted thread actually said, plus how to work around the worst of it."
---

I get it. You saw Tailscale, you clicked "install," and ten minutes later your homelab is reachable from a coffee shop in Bangkok. Magic. Free magic.

But that r/selfhosted thread — "Any downsides to tailscale? Seems like an incredible amount of value for free" — had some real talk buried under the hype. I've run this thing for two years across three VPSes and a Raspberry Pi. Here's what I wish someone told me.

## The "free" part has a ceiling

Tailscale's free tier gives you 100 devices and 3 users. Sounds generous until you realize your phone, laptop, server, and that ESP32 you're tinkering with all count. One commenter in the thread said they hit the limit at 47 devices because they added every container as a separate node.

The fix: run Tailscale on the host, not inside every Docker container. Use `--net=host` or route traffic through the host's tailnet. You don't need 12 nodes for 12 services.

## Control plane dependency — the real gotcha

Here's the thing nobody mentions in the first five minutes: Tailscale uses a coordination server to exchange keys. If their control plane is down, your existing connections keep working (thanks to DERP relays and cached state), but *new* devices can't join.

One user in the thread said their tailnet broke during a Tailscale outage and they couldn't SSH into a box to fix it. That's a chicken-and-egg problem.

**The workaround:** set up a fallback. Keep a plain WireGuard config as a backup for your critical boxes. It takes 15 minutes and saves you from a bad day. Or self-host the control plane with Headscale — it's more work, but you own the whole stack.

## DERP relays are slow, and you'll hit them

Tailscale's magic is NAT traversal. When it works, you get direct peer-to-peer connections. When it doesn't — symmetric NAT, CGNAT, hotel Wi-Fi — you fall back to DERP relays. Those are Tailscale's servers, and they're not fast.

I've seen throughput drop from 900 Mbps to 40 Mbps over DERP. Fine for SSH, painful for streaming your Jellyfin library.

Check your connection path with:

```bash
tailscale status
tailscale ping <hostname>
```

If you see `via DERP`, you're relaying. You can force direct connections with `tailscale up --netfilter-mode=off` in some cases, but honestly, your mileage may vary. The community is genuinely split on whether this is a Tailscale problem or an ISP problem.

## The ACL learning curve

Tailscale's ACLs are powerful — too powerful for most people. The default is "everything can talk to everything," which is fine for a homelab but terrifying if you're exposing services to friends or family.

The thread had a guy who accidentally exposed his NAS to his entire tailnet because he didn't understand how `autogroup:internet` worked. The syntax is JSON-based and the docs assume you know what you're doing.

Start with this basic ACL that only allows SSH and HTTPS:

```json
{
 "acls": [
    {"action": "accept", "src": ["autogroup:member"], "dst": ["autogroup:self:22", "autogroup:self:443"]}
 ]
}
```

Test it. Break it. Fix it. That's the learning curve.

## The magic key problem

Tailscale auth keys can expire, and if you're using them in Docker Compose or scripts, you'll wake up one day to find your containers can't connect. The thread had a guy who spent an hour debugging why his Proxmox backup script stopped working — expired key.

Use ephemeral keys for containers and set a reasonable expiry. Or use `tailscale up --authkey` with a key that never expires, but understand the security tradeoff.

## What I'd actually do differently

If I were starting fresh today, I'd still pick Tailscale. The value is absurd — free, zero-config, works on everything from a Raspberry Pi Zero to a Hetzner VPS. But I'd:

1. Keep a WireGuard fallback for critical infrastructure
2. Run it on the host, not in containers
3. Learn ACLs before inviting anyone else to my tailnet
4. Accept that DERP relays are a fact of life and design around them

## FAQ

**Is Tailscale really free forever?**
The free tier is genuinely free for personal use — 100 devices, 3 users. No credit card required. But if you're running a business or need SSO, you'll pay $6/user/month. The community is split on whether the free tier will shrink, but it's been stable for years.

**Can I use Tailscale with Docker?**
Yes, but run it on the host or use the official container image with `--net=host`. Don't create a node per container — you'll hit device limits fast.

**Does Tailscale work on ARM devices?**
Yes, and it's one of the few tools that just works on Raspberry Pi and ARM VPSes. I haven't tested it on every SBC, but the official images cover most architectures.

---

*This post was inspired by a real discussion on r/selfhosted. Your experience may differ — that's the fun of self-hosting.*