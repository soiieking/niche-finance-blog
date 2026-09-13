---
title: 'PSA: Using Pinned Versions of qmcgaw/Gluetun? Here’s What You Need to Know'
date: '2026-09-14 06:00:03+08:00'
draft: false
tags:
- selfhosted
- docker
- vpn
- networking
summary: Gluetun is awesome for self-hosting VPNs, but pinned versions can trip you
  up. Fix your setup before you break something.
---

## So, You’re Pinned to a Gluetun Version

If you’re running `qmcgaw/gluetun` on Docker (or Podman or whatever flavor you’re into), let’s talk about pinned versions for a sec. Specifically, if you’re the kind of person running `docker pull` with a specific tag like `:v2023.10.30`, pay attention.  

Someone in r/selfhosted recently learned this the hard way: **old Gluetun versions don’t always play nice with new provider configs.** Yep, your VPN provider (like Mullvad, NordVPN, ProtonVPN, etc.) changes endpoint IPs or certs, and suddenly your Gluetun container is a brick. No connection, no logs that actually tell you what’s wrong. Just you, scrolling like mad through Docker logs and Googling DNS errors. Sound familiar?  

## What’s Really Happening Here?

Gluetun is built around staying up to date. The whole point of using it is not having to wrestle directly with VPN configs or OpenVPN clients. But if you’re on an older version—maybe because you pinned to `:latest` months ago, or because you thought `v2023.05` was "stable"—you’re not getting those sweet, sweet updates. And VPN providers shift constantly. They add new servers, rotate certificates, and tweak strategies to dodge ISP restrictions.  

Without updated Gluetun binaries or built-in config files, your pinned version? Dead in the water. Example: Mullvad started requiring new server IPs mid-2023. Pins to anything older than `v2023.08.22`? Boom, broken. Even with other providers, Gluetun itself is an actively developed project, and outdated versions often miss stuff—like smaller DNS leak fixes or OpenVPN timeouts.  

## The Fix: How to Unpin (Or At Least Pin Smarter)

### Step 1: Stop Blindly Patching `:latest`

I get it. Updating `:latest` feels dangerous for something you use daily. God forbid it updates mid-week while you’re on a work call. So here’s what I recommend: use a rolling tag, but **test it first in a staging container**.  

```sh
docker pull qmcgaw/gluetun:v2023.10.30
docker run --rm --name testing-gluetun qmcgaw/gluetun:v2023.10.30
```

Check the logs for issues. If it spins up and connects cleanly, you’re probably good. If not, at least you’ve only killed a test container, not the main one routing your Plex downloads.  

### Step 2: Automate With Watchtower (If You Dare)

For the brave, tools like Watchtower can handle auto-updates for Docker containers. Add Watchtower to your stack:  

```sh
docker run -d --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower
```

By default, it’ll update everything, so pin `watchtower`’s scope to just Gluetun if you want more control:  

```sh
docker run -d --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower qmcgaw/gluetun
```

Caveat: You’re trusting Gluetun to not push breaking changes. Most selfhosters swear by this, but it’s not foolproof. I’d still suggest manually reviewing changelogs occasionally.  

### Step 3: Version Tags, But Stay Current-ish  

If Watchtower feels risky (it can be), pin by minor versions instead of locking down to a patch. Something like this:  

```sh
docker pull qmcgaw/gluetun:v2023.10
```

This keeps you within the same "major" features but gives some room for bug fixes and compatibility updates. Just keep an eye on [Gluetun’s releases page](https://github.com/qdm12/gluetun/releases) to know when a new minor version drops.  

### Bonus: Run Gluetun’s Healthcheck

Docker’s built-in `HEALTHCHECK` can cut down debugging time. Gluetun’s example:  

```sh
HEALTHCHECK CMD curl --fail http://localhost:8000/v1/status || exit 1
```

This adds a fail-safe if your VPN is down. Pair it with a service like Portainer to auto-restart containers when health checks fail.  

## Does This Apply Across All Architectures?

Short answer: mostly, yes. I’ve tested this setup on x86_64; ARM should be fine if you’re running a Pi or something. But Gluetun handling can vary across VPN providers—some have quirks (Windscribe especially comes to mind). For obscure VPNs, test before rolling out updates.  

## FAQs

### Why Does My Gluetun Exit Node Have DNS Issues After an Update?  
If your DNS goes haywire after updating, verify the `DNS_OVER_TLS` or `DOT_PROVIDERS` setting in your config. Some older configs become incompatible with newer versions. Reset to defaults:  

```sh
DNS_OVER_TLS=on
DOT_PROVIDERS="cloudflare"
```

Then restart:  

```sh
docker restart gluetun
```

### Can I Run Gluetun Without Docker?   
Yes, but why? Honestly, it’s meant for containerized deployment. If you hate Docker, go with Podman—same setup applies 99% of the time.  

### What's the Minimum Spec I Need for Gluetun?  
Not much. Even a 1-core VPS with 256 MB RAM can handle a single tunnel. But for more routes or heavy torrenting, bump up to 1 GB RAM and better IOPS. Hetzner CX11 ($4-5/mo) is an excellent choice.  

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why Does My Gluetun Exit Node Have DNS Issues After an Update?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Check the DNS_OVER_TLS or DOT_PROVIDERS setting in your Gluetun config. Some old configs break with new builds. Reset to defaults and restart."
      }
    },
    {
      "@type": "Question",
      "name": "Can I Run Gluetun Without Docker?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, but it’s not recommended. Docker (or Podman) packages everything for you, so you’re not tweaking OpenVPN manually."
      }
    },
    {
      "@type": "Question",
      "name": "What's the Minimum Spec I Need for Gluetun?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "A 1-core VPS with 256 MB RAM is enough for basic use. For more, go with at least 1 GB RAM. Hetzner CX11 at $4-5/month is a solid option."
      }
    }
  ]
}
</script>
