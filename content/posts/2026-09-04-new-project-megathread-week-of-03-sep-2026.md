---
title: 'New Project Megathread Highlights: Week of 03 Sep 2026'
date: '2026-09-04 14:00:03+08:00'
draft: false
tags:
- selfhosted
- projects
- open-source
- sysadmin
summary: 'This week''s r/selfhosted standouts: a new Jellyfin ''killer,'' a self-hosted
  YouTube clone, and why Nginx might finally be toast.'
---

## Another day, another megathread. Here’s what’s catching fire on r/selfhosted this week:

### **1. Final Cuts: Is Axolotl the New Jellyfin Alternative?**
User u/StaticVoidDev dropped a jaw-breaker on Axolotl Media Server, calling it “Jellyfin meets Plex but without the usual Docker hell.” Big claim. Axolotl’s been picking up buzz lately, mostly because it leans into GPU transcoding *out of the box*. No plugins, no scripts—just good ol’ “it works” functionality. 

Here’s the kicker: it’s built on Rust, so performance is naturally snappy, but the question of long-term support looms. As u/OverkillConfig rightly pointed out, “It’s v0.4.1—so calling it stable is wishful thinking right now.” Fair, but for folks who hate tinkering, this one’s worth watching.

Recommendation? If you’re running something predictable like Unraid or a Proxmox setup, give it a spin. Jellyfin isn’t going anywhere, but sometimes new blood kicks the old guard into innovating faster. Or, you know, imploding under a rewrite.

---

### **2. Pipes You Didn’t Know You Needed: TubeArchivist as a Self-Hosted YouTube**
Yes, *another one*. We’ve seen our share of YouTube archivers (yt-dlp is a classic), but TubeArchivist deserves some love this week. Not only does it download videos, but the search and tagging functionality puts most frontend GUIs to shame. 

u/ArchTheArchivist summed it up perfectly: “It’s like having a tiny, manageable subset of YouTube without the rage-inducing algorithm.” With Elasticsearch under the hood, it’s surprisingly fast—even when indexing hundreds of videos. Just don’t try this on an SBC unless you enjoy suffering. You want minimum 4GB of RAM and a $10 VPS isn’t gonna cut it.

Also? Throw in Readarr as a companion, and now you’ve got a setup to keep tabs on niche creators you love while binging offline. Just don’t @ me if your storage balloons to 4TB overnight.

---

### **3. Nginx is Dead. Long Live Caddy?**
u/sysdmin_life went on a tear about why they moved 20+ projects from Nginx to Caddy, and honestly, it tracks. With Let’s Encrypt support baked in, Caddy simplifies reverse proxies like no one’s business. The quote that sold me? “Copy the binary anywhere, fire it up, and you’ve got HTTPS in under 30 seconds.” 

The Caddyfile syntax isn’t everyone’s jam, though. u/WebstackDown chimed in saying, “I’d rather write a 500-line Nginx conf than trust magic.” Fair enough. But the fact that response times are a solid 25% faster (benchmarked on low-traffic Flask/Node apps) and it scales well has me convinced. 

Don’t rip out your Nginx+Lua stack just yet—but, if you’re bootstrapping? Definitely look into Caddy unless you have reasons tied to legacy systems.

---

### **4. r/selfhosted To-Do of the Week: Syncthing for Your Photo Backup Hell**
This one’s more of a PSA than a project deep dive, but HOLY HELL, just use Syncthing for your family’s photo situation already. Seriously. Google Photos is an easy trap to fall into, but Syncthing offers the kind of backup transparency you actually *trust*. 

u/TimeWarpedPixie hit the nail on the head: “I set it up between my NAS and my spouse’s phone, and we haven’t had to think about backups again. Zero maintenance.” Start with version v1.27.0 and, yeah, it can handle your typical “5 devices, 100k images” scenario. Just remember to still run backups off your NAS—Syncthing != a safety net.

---

## Honorable Mentions:
- **Firezone VPN**: A WireGuard wrapper that’s so smooth u/ITGuy1979 described it as “like Tailscale but I actually control the infra.” 
- **Monica CRM**: For anyone who needs a replacement for those “who-the-hell-is-this-contact-again” moments in their address book. Open source, clean UI, slightly buggy.
- **Hetzner vs. Contabo (again)**: Endless debate rages over whether Hetzner justifies costing 2x as much. Spoiler: Yes, if you want uptime.

---

### FAQs

#### **1. Why isn’t Axolotl production-ready?**
It’s early days—features are still being implemented and bugs likely exist. Think of it as a beta replacement, not something to trust for *critical* use.

#### **2. How much RAM does TubeArchivist need?**
The general rule of thumb is 4GB minimum. Elasticsearch *loves* to eat memory, so budget more if you’re indexing a ton of channels.

#### **3. Is Syncthing hard to set up?**
Not at all. Most users report being up and running in under 20 minutes. Just copy the config QR code between devices and tweak paths to avoid duplication.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why isn’t Axolotl production-ready?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "It’s early days—features are still being implemented and bugs likely exist. Think of it as a beta replacement, not something to trust for critical use."
      }
    },
    {
      "@type": "Question",
      "name": "How much RAM does TubeArchivist need?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "The general rule of thumb is 4GB minimum. Elasticsearch loves to eat memory, so budget more if you’re indexing a ton of channels."
      }
    },
    {
      "@type": "Question",
      "name": "Is Syncthing hard to set up?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Not at all. Most users report being up and running in under 20 minutes. Just copy the config QR code between devices and tweak paths to avoid duplication."
      }
    }
  ]
}
</script>
