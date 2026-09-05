---
title: 'NetBird 0.78: Draft Mode Brings Visual Network Building to Self-Hosters'
date: '2026-09-05 08:00:04+08:00'
draft: false
tags:
- selfhosted
- vpn
- linux
- networking
summary: NetBird 0.78 introduces a visual Draft Mode for network setup. It's cool,
  but does it live up to the hype for self-hosters?
---

NetBird just dropped version 0.78, and the highlight is Draft Mode for the Control Center. The pitch? You can now visually build your private network before you push changes live. For anyone managing a bunch of VMs or edge devices, this sounds slick. But does it actually hold up, or is it just pretty UI sugar?

I’ve messed around with NetBird a fair bit, and while it’s no WireGuard-killer (nothing is), it’s great if you want to glue multiple locations together seamlessly. The project’s been laser-focused on usability since day one, and Draft Mode feels like a continuation of that. The question is whether this specific feature pushes NetBird past "neat experiment" territory for most self-hosters.

## What *is* Draft Mode?

Draft Mode lets you map out your network changes visually before applying them to your running setup. Think staging, but with a diagram. This feature lives in the Control Center, NetBird’s browser-based admin panel, and it’s part of their push to make networking accessible even if you’re not a CLI junkie.

The idea is simple: instead of breaking your network because you typoed an endpoint or forgot a policy, you can sketch it out, tweak, and then commit. Want to add a new device on Hetzner with a specific peer-to-peer policy? Model it first. Need to remove a failed DigitalOcean node? Simulate how that impacts routing. It's a sandbox with guardrails for people who both love tinkering and fear downtime.

## Who’s gonna love this?

This is solid for small teams or home-labbers with complex setups. If you’re running a mix of locations—say, a Nextcloud instance at home, a Jellyfin server on a cheap VPS, and a Pi-hole in the cloud—Draft Mode makes wrangling all that simpler. You don’t need deep networking chops to model and troubleshoot connectivity.

One thread comment from r/selfhosted summed it up perfectly: "NetBird is like Tailscale for people who don’t trust closed platforms. Draft Mode is icing if you run multiple subnets." If you’ve ever wanted better visibility into how your devices talk to each other without setting up Grafana dashboards or dumping iptables logs, this is made for you.

## But… is it worth the hype?

Kinda. It’s a great feature, but let’s not kid ourselves—this isn’t going to be game-changing for everyone. First off, NetBird itself is overkill if a simple WireGuard setup meets your needs. If you just want a secure tunnel between your house and a VPS, skip NetBird entirely and roll your own with a 200-line bash script.

Draft Mode is also, unsurprisingly, a first draft itself. It’s not going to magically make you a networking expert. If your routing tables are already a mess or you’re introducing asymmetric policies, the UI won’t save you from yourself. Consider it a tool to think out loud without wrecking production, not a fix-all.

It’s also worth pointing out that NetBird’s resource overhead is slightly higher than WireGuard since you’re running a control plane on top of standard networking. While it’s fine for most modern setups, it might be heavier than you want on resource-constrained devices like older Raspberry Pis. I haven’t tested 0.78 extensively on ARM yet, so your mileage may vary.

## Alternatives to Consider

Not sold? Here are a few other tools to look at depending on what you need:

1. **Tailscale**: Dead simple, relies on WireGuard, but leans hard on closed-source infrastructure. If the control layer bothers you, move on.
2. **Headscale**: Fork of Tailscale minus the proprietary bits. Definitely more DIY but great if you want Tailscale-like behavior without cloud dependence.
3. **WireGuard + Ansible**: Old-school but gold-standard. If you’re managing under 10 devices and want absolute control, build it yourself.

NetBird lives in that sweet spot between total control and absurd convenience, and Draft Mode leans into making life easier. But if you’re the kind of person already running a Proxmox cluster or Kubernetes, you might find Draft Mode's simplicity underwhelming.

## Final Thoughts

NetBird 0.78’s Draft Mode is a cool addition, and it reinforces the project’s commitment to making networking understandable for people who aren’t hardcore sysadmins. Is it going to revolutionize self-hosted networking? No. But for small setups and home labs, it’s a nice quality-of-life upgrade that makes NetBird even more user-friendly.

---

### FAQ

#### How does NetBird compare to Tailscale?  
NetBird is open-source and doesn’t rely on proprietary infrastructure, while Tailscale uses closed-source control servers. They’re both designed to simplify networking with WireGuard as the backbone, but if you care about self-hosting, NetBird is the way to go.

#### Does Draft Mode require extra setup?  
Nope, it’s baked into the Control Center UI in version 0.78. If you’re already running NetBird, just upgrade and the feature’s there.

#### Will it work on ARM devices like Raspberry Pi?  
It should, but I haven’t stress-tested Draft Mode specifically on ARM. NetBird itself has ARM builds, but resource usage might be an issue on underpowered hardware.
