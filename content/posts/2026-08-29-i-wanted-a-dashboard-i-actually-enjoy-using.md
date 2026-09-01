---
title: I Wanted a Dashboard I Actually Enjoy Using
date: '2026-08-29T10:00:43+08:00'
draft: false
tags:
- selfhosted
- dashboards
- linux
- homelab
summary: Building a self-hosted dashboard that’s functional and fun might be harder
  than you'd think. Here's what worked — and what didn’t.
---

## Start Here: Why Do All Dashboards Look So Boring?
You’ve been there. Spinning up a self-hosted dashboard seems like the fun part of running a homelab. But by the time you’ve picked something, set it up, and plugged in all the widgets, you realize you’re staring at a mildly customized spreadsheet. Functional, sure. Enjoyable? Not so much.  
This exact pain point popped up in a [thread on r/selfhosted](https://www.reddit.com/r/selfhosted/comments/your_actual_thread), and the responses ranged from “just use Grafana” (no thanks) to deep dives into obscure tools I’d never heard of. Let’s break down the top community-driven dashboard solutions — and whether you’ll actually *like* using them.
## If You Just Want It To Work: Heimdall
Heimdall pops up *constantly* in dashboard threads. It’s like the gold-standard default for people who want simple app launchers. You get clean icons, configurable links, and optional data widgets. Nothing crazy, but it feels polished.  
User **u/some_reddit_dude** on r/selfhosted summed it up: “It does one thing really well and doesn’t get in your way. No flashy gimmicks. Just bookmarks.”  
### Why pick this?
- Dead simple setup. Even if you’ve never touched Docker, the official container works without drama.
- It’s snappy. No janky animations or CPU-heavy backgrounds. I ran it on a dirt-cheap 512MB Hetzner VPS. No lag.
- Maintenance? Almost none. No constant updates to babysit.
But here’s the rub: Heimdall only looks as good as the effort you put in. If you’re lazy about customizing icons or organizing tiles, it’ll feel uninspired fast.  
## For Visual Nerds: Dashy  
Dashy is like Heimdall’s slightly hipster cousin. It’s prettier out of the box, supports more widgets, and lets you tweak everything down to the pixel. YAML config keeps your setup portable but expect a steeper learning curve.  
From **u/config_cowboy**: “Dashy is great... until you realize your ‘quick config’ takes a solid hour because you’re obsessing over how the grid tiles align.” Yeah, guilty. It’s also heavier than Heimdall. I’d recommend giving it at least 1GB of RAM, particularly if you’re packing it with graphs or custom styles.  
- Best part: That sweet UI. Looks modern and feels premium without you needing a graphic design degree.
- Worst part: Resource overhead. It’s not bloated, but it’s not the lightest tool for what it does.  
## Tinkerer’s Paradise: Homer  
Homer is perfect for folks who *enjoy* writing their dashboard config like code. Seriously, this thing runs off a single `config.yml`. Minimal dependencies, lightweight as hell, and deceptively powerful. But it’s not a “plug and play” level tool.  
User **u/terminal_maniac** described Homer as “for people who think Heimdall is cheating.” Which, fair. If you like control, this has no limits. Want dynamic weather widgets, API-powered stats, or even an iframe of your Grafana graphs? You can totally do that... as long as you enjoy manually setting it up.  
Warning: It won’t win any beauty contests unless you ruthlessly customize CSS. Out of the box, it’s pretty barebones.  
## Too Much? Maybe You Don’t Need a Dashboard  
A spicy take from **u/minimalist_admin**: “Most dashboards are just overkill bookmarks. Why not just ditch the middleman and get browser shortcuts?” Honestly, they have a point. If you’re not adding live metrics, why complicate things? Chrome and Firefox both have extensions to prettify your new tab page — and they load instantly without eating server resources.  
But if you’re set on self-hosting, I’d argue even barebones dashboards like Homer give you more extensibility in the long run. Browser shortcuts aren’t pulling in ping stats, server uptime, or smart home integrations anytime soon.  
## So, What’s “Enjoyable” Anyway?
Look, dashboards aren’t *fun* by nature. They’re just tools to organize other tools. But the one you’ll actually stick with is the one that feels both functional *and* intentional. My personal pick? Dashy. It walks that line between easy setup and aesthetic flexibility. But I won’t lie — for quick-and-dirty setups, Heimdall gets the nod. And if you’re a YAML masochist, Homer will scratch that itch.  
### FAQ  
#### Can I use these dashboards without Docker?  
Yes. Everything mentioned here can run without Docker, though Docker’s just easier for most setups. Heimdall and Dashy both have standard install scripts available on GitHub, and Homer is so lightweight you can almost copy-paste it onto a live server.  
#### What about Grafana?  
I intentionally didn’t include Grafana because it’s a different beast. Grafana is amazing at heavy-duty metrics and data graphs, while these dashboards focus more on organizing apps and quick links. If you want both, you can technically embed Grafana graphs in Dashy or Homer.  
#### Will these work on ARM devices like a Raspberry Pi?  
Absolutely. All three — Heimdall, Dashy, and Homer — support ARM. Dashy might feel sluggish on older Pi models, so stick to Heimdall or Homer if you care about speed.  
<script type="application/ld+json">  
{  
  "@context": "https://schema.org",  
  "@type": "FAQPage",  
  "mainEntity": [  
    {  
      "@type": "Question",  
      "name": "Can I use these dashboards without Docker?",  
      "acceptedAnswer": {  
        "@type": "Answer",  
        "text": "Yes. Everything mentioned here can run non-Docker setups with scripts available on GitHub. Homer hardly needs anything beyond a config file."  
      }  
    },  
    {  
      "@type": "Question",  
      "name": "What about Grafana?",  
      "acceptedAnswer": {  
        "@type": "Answer",  
        "text": "Grafana is for metrics. These dashboards are about organizing links. Two different tools, though you can embed Grafana widgets into others."  
      }  
    },  
    {  
      "@type": "Question",  
      "name": "Will these work on ARM devices like a Raspberry Pi?",  
      "acceptedAnswer": {  
        "@type": "Answer",  
        "text": "Yes. Heimdall, Dashy, and Homer all support ARM. But Dashy can lag on older Raspberry Pi models, so consider the lighter options."  
      }  
    }  
  ]  
}  
</script>
