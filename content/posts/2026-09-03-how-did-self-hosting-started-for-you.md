---
title: 'How I Got Hooked on Self-Hosting: A Cautionary Obsession'
date: '2026-09-03 10:00:07+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: From a simple Pi-hole install to a full-blown homelab rabbit hole — how one
  tinkerer's love affair with self-hosting began.
---

## How Self-Hosting Hooked Me (And Probably You Too)  

It started with one little project. For me, it was Pi-hole. Ad-blocking at the network level sounded pretty badass—like I was Neo dodging ads instead of bullets. I had an old Raspberry Pi 3B lying around (remember when they cost $35?), so why not? Spoiler: the install took 20 minutes, but troubleshooting my broken DHCP later? Hours. Still, it worked. And oh, the dopamine hit of seeing all those blocked trackers. That’s how they get you.

For a lot of folks on r/selfhosted, it’s the same story. Someone in the thread mentioned Jellyfin as their gateway drug—a free and open-source Netflix clone. Another said they just wanted to stop paying $20/month for Google Drive and found Nextcloud. The starting point varies, but the pattern is the same: one service leads to another, and the next thing you know, you're SSHing into your VPS at 1 AM because WireGuard mysteriously stopped working (it was the kernel upgrade—again).  

## Why Self-Hosting Feels Amazing  

You feel in control. That’s the #1 reason people get hooked. Your data, your rules. Running your own Nextcloud instead of paying Google or Apple? Feels good, man. Hosting your own photo gallery with Immich and realizing it uses a tenth of the RAM of PhotoPrism? Chef’s kiss.  

There’s also the brain-itch part of it. You’re solving problems, learning weird Linux commands, and smashing your head against poorly documented Docker configs. Every success, no matter how small—binding a container to the right port or fixing your Nginx reverse proxy—gives you a hit of “I am the sysadmin god of my castle.”

And let’s be honest: part of the appeal is showing off. Posting your slick Homer dashboard to Reddit: priceless. Or the flex of saying, “Nah, man, I rolled my own Bitwarden instance. Totally worth the effort.” Yeah, your friends don’t care. But we do.

## The Trap: One Project Becomes 20  

Here’s the thing about self-hosting—once you start, it doesn’t stop. The moment you realize "Hey, I could also self-host X," you’ve already lost. My Pi-hole turned into a full media server stack (Jellyfin, Radarr, Sonarr). Then home automation with Home Assistant. Then VPNs, file syncing, note-taking apps, and a Grafana dashboard to monitor it all.  

And yeah, I broke *all* of it at some point. Upgrading a service is easy until it isn’t. One time I updated Foundry VTT (for hosting tabletop games), only to nuke all my plugins. Lesson learned: never skip backups.

Then there’s the hardware escalation. That Pi 3B? Nope. Outgrew it in a month. Bought an old OptiPlex 9020 on eBay for $100. Quad-core i7, 16GB RAM, 1TB HDD. Perfect, right? Until I wanted ZFS because Reddit told me it’s amazing (it is, but it’ll eat your RAM alive). Now I’m eyeing second-hand servers on Craigslist and calculating how much my power bill can take.  

## Why Now? Cloud Fatigue  

Self-hosting feels especially relevant today because people are fed up. Cloud subscriptions add up fast, and we’ve all been burned by a "free" service that got shuttered, locked features behind a paywall, or jacked up prices (looking at you, Heroku and Dropbox).  

Data privacy is a big driver, too. Who actually trusts Alexa or Google Home anymore? Self-hosted Home Assistant on Zigbee sticks doesn’t just beat Big Tech; it *smashes* it. Plus, why would I pay Google $10/month for 2TB of storage when I can shove the same data into my own Nextcloud, running on a NAS with $200 worth of drives?  

The trend’s amplified by better tools. Docker containerized the chaos. Portainer makes it clicky. Distros like Proxmox and TrueNAS take the edge off bare-metal nightmares. You still need patience—and a willingness to Google—but the barriers are lower.  

## OK, But Is This for Everyone?  

Honestly? No. If you just need a Plex server, stick with a paid VPS like Linode or Hetzner. Cheaper than running a power-hungry box 24/7. Self-hosting *at home* is overkill if you live somewhere with flaky power or limited upload speeds.  

Also, the time sink is real. Every new app you host, every update, every "minor" bug you resolve eats hours. It's a fun hobby if you like tinkering, but don’t sell yourself the lie that it’s turnkey. This stuff breaks—a lot.  

That said, if you're even 1% curious about hosting your own stuff, I’d say try it. Start with a free VPS from Oracle's cloud or throw something lightweight like Heimdall on an old laptop. Worst case, you learn a few Linux commands. Best case, you find a new obsession.  

---

### FAQ  

#### What budget should I start with?  
You can get started for $0 if you use Oracle’s free tier—Ampere ARM instances are solid (though I haven’t tested everything on ARM, so proceed cautiously). For hardware at home, $50–$150 for a used OptiPlex or similar works.  

#### Can I self-host with limited technical knowledge?  
Yes, but be ready to learn. Tools like Docker-Compose and Portainer simplify setup, but you *will* hit weird problems. The r/selfhosted wiki is your best friend.  

#### What’s the best "starter" project?  
Pi-hole is a classic. Lightweight, useful, and easy to install. Nextcloud is great too, but has a steeper learning curve—expect to fight SSL setups if you want remote access.  

<script type="application/ld+json">  
{  
  "@context": "https://schema.org",  
  "@type": "FAQPage",  
  "mainEntity": [  
    {  
      "@type": "Question",  
      "name": "What budget should I start with?",  
      "acceptedAnswer": {  
        "@type": "Answer",  
        "text": "You can get started for $0 if you use Oracle’s free tier—Ampere ARM instances are solid (though I haven’t tested everything on ARM, so proceed cautiously). For hardware at home, $50–$150 for a used OptiPlex or similar works."  
      }  
    },  
    {  
      "@type": "Question",  
      "name": "Can I self-host with limited technical knowledge?",  
      "acceptedAnswer": {  
        "@type": "Answer",  
        "text": "Yes, but be ready to learn. Tools like Docker-Compose and Portainer simplify setup, but you *will* hit weird problems. The r/selfhosted wiki is your best friend."  
      }  
    },  
    {  
      "@type": "Question",  
      "name": "What’s the best \"starter\" project?",  
      "acceptedAnswer": {  
        "@type": "Answer",  
        "text": "Pi-hole is a classic. Lightweight, useful, and easy to install. Nextcloud is great too, but has a steeper learning curve—expect to fight SSL setups if you want remote access."  
      }  
    }  
  ]  
}  
</script>
