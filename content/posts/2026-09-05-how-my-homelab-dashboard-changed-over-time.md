---
title: How My Homelab Dashboard Evolved (and Why Yours Will Too)
date: '2026-09-05 16:00:03+08:00'
draft: false
tags:
- selfhosted
- homelab
- dashboard
- linux
summary: 'From Chaos to Grafana: A brutally honest look at how my homelab dashboard
  grew from fun experiments to something actually useful.'
---

## Why Start Simple When You Can Overcomplicate Everything?

When I first spun up my homelab, my "dashboard" was really just a massive mess of browser bookmarks. Want to check Pi-hole stats? Open tab 5. Jellyfin server up? Tab 12. Proxmox? That’s… actually running in a different browser. It was chaotic but functional, like a digital junk drawer.

But, naturally, I wanted more. Enter Organizr. This thing gets hyped a lot in **r/selfhosted** for centralizing all your apps in one slick UI. At first glance, it's great: easy to install (Docker Compose FTW), supports tons of services, and it’s just fun showing off a grid of app icons to friends who ask, "Wait, you host your own Netflix?"

Still, Organizr fell apart for me as I started adding monitoring tools. It was never meant to display metrics, and shoehorning something like a Grafana graph into a frame felt... wrong. So, onto the next shiny thing.

## Grafana: The Tool That Made Me Care About Metrics

Grafana isn't perfect. It’s overkill for a private homelab unless you’re a data nut, but my god, *it works*. With a couple Prometheus exporters (node-exporter, Docker exporter, and the obligatory Loki for logs), I suddenly had a living, breathing dashboard. Things I could actually *act on*:

- CPU temps spiking? Time to clean the fans again.
- Memory on my Raspberry Pi 4 maxed out? Right, the Frigate container doesn’t like 4GB of RAM.
- That weird load average? Yeah, "just one more Plex transcode" stressed my NUC harder than I thought.

Even better, Grafana forced me to be intentional about what I cared about. It's not a slap-everything-on-the-wall situation. Instead, you curate: Do I actually need 1-second updates? (No.) Do I care about disk throughput over the last year? (Also no, but the graph looks cool.)

That’s the beauty of Grafana—it’s an endless well you don’t have to jump entirely into. Import a few dashboards, tweak them as you go. Barebones at first; fancy last.

## Did Anyone Say Homer? Because They Should.

Fast forward a bit, and my Grafana addiction needed balancing out. Not everything needed to live there. For quick app management—click to launch Portainer, Jellyfin, Nextcloud, etc.—I wanted simplicity. Enter Homer.

Homer is hands-down my favorite dashboard for most homelab setups. No database needed, minimal config, and clean as hell. Think of it as a LEGO starter kit that looks nice out of the box but still lets you customize to your heart's content. The `config.yml` is *chef's kiss*. Want different icons? Change three lines. Want groups by category? Easy.

I replaced Organizr with Homer and never looked back. It's perfect for people who don’t need bells and whistles. Just launch your stuff and move on. Plus, it pairs beautifully if you self-host tools like Fail2Ban logs on your dashboard—I use custom links to point to my setup scripts, too. Function over form—but it still looks *good.*

## Why This Stuff Matters

Dashboards, by themselves, don’t *do* anything. They don’t magically stabilize your Nextcloud or make your MediaGoblin less weird. But they make your life easier when things go wrong.

Having everything in one place—up/down checks, resource graphs, quick app links—turns your homelab from a chaotic science fair project into something that vaguely resembles a streamlined spaceship command center. That’s not *nothing*. And it gets you obsessing about uptime in the best way.

Plus, as evidenced by Reddit fights weekly, dashboards are *fun*. You don’t have to spend $200 on a Raspberry Pi because "I need Prometheus to run 24/7," but hey, some of us will.

## Where Things Stand Now

Right now, my dashboard stack is hybrid: Homer for management, Grafana for stats. I haven’t felt the need to try Heimdall, but that’s me. Meanwhile, Portainer's embedded dashboard gets its own spot in Homer because sometimes you just want to troubleshoot containers *right there*. Homer keeps it opening in a new tab—clean separation. No surprises.

It’s not perfect. Grafana’s resource usage can balloon if you don’t define your data retention properly (looking at you, Loki). Homer’s YAML setup isn’t beginner-friendly in 2026 when people are spoiled by GUIs. But both tools get the job done better than my original "5,000 browser tabs" system.

Your dashboard journey will evolve, because *everything* in homelabbing evolves. Today’s Docker user is tomorrow’s K8s tinkerer. Just don’t get sucked into building something so massive that you forget to actually use your servers. That’s the trap.

---

### FAQ

#### What’s the difference between Organizr, Homer, and Heimdall?

Organizr does a bit of both "dashboard" and "portal"; it has user management and some plugin support. However, loading it with too many services can make it sluggish. Homer and Heimdall focus on simplicity: if you just want links and pretty icons, pick one of those. Homer is YAML-based and lightweight; Heimdall offers more UI options, especially if YAML sounds scary.

#### Is Grafana necessary?

Not at all. For *most* homelab setups, a good status page (like Uptime Kuma or even your NAS’s built-in interface) is "enough." Grafana is for people who enjoy slicing data: think logs, CPU trends, or heatmaps. If that’s not you, skip it.

#### How much does this stuff cost to run?

Homer and Grafana themselves are free. Resource-wise, Homer is tiny (runs on a cheap VPS or Pi). Grafana + Prometheus + Loki needs more: my stack averages 500MB RAM + ~2% CPU on a bare-metal NUC. Your mileage definitely varies, especially if you’re logging like crazy.
