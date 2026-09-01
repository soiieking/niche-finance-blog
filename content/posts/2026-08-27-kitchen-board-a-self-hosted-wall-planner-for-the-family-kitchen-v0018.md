---
title: 'Kitchen Board v0.0.18: Self-Hosting a Family Wall Planner'
date: '2026-08-27T20:00:36+08:00'
draft: false
tags:
- selfhosted
- home automation
- organization
- linux
summary: Turn your kitchen wall into a digital planner with Kitchen Board — open-source,
  self-hosted, and built for simplicity.
---

So, you’ve got a family and a chaotic fridge full of sticky notes. Enter [Kitchen Board](https://github.com/user/project), the v0.0.18 release of an open-source wall planner meant to replace calendar apps your family won’t actually use. It's not perfect, but hey, nothing is. Here’s how you set it up and what to expect.
Spoiler: if you need Google Calendar sync or mobile bells and whistles, this isn’t it. But if you want a no-pressure, local-first dashboard that screams “community project,” Kitchen Board may surprise you.
## Why Use Kitchen Board?
Honestly? I’d pin this on two camps:  
1. **You want control**: Sick of managing 73 cloud logins for your family? Here’s your logout button.  
2. **You hate overkill**: No AI calendar assistants, no inbox integrations, just a clean wall display on a Raspberry Pi or an old tablet.
A commenter on r/selfhosted (@localroot) called it “old-school in the best way.” Exactly. It’s simple and home-grown. But it’s also still pre-1.0 at v0.0.18, so don’t expect polish. Expect growing pains. 
## Quick Setup
Let’s keep it real: this runs fast but assumes you’re sort-of competent with Docker. If copying commands makes you cry, maybe hit pause.
### Step 1: Prep Your Environment  
Kitchen Board at v0.0.18 is a lightweight Node.js app bundled with a minimal front-end. It’ll run fine on a Raspberry Pi (4GB RAM is plenty) or a low-tier VPS like Hetzner’s CX11 (€4/month). But for the kitchen, go cheap and local.
- **OS**: Ubuntu 22.04 or similar  
- **Docker**: Any modern 23.x release works here.  
- **Free Port**: 3000 default, tweak as needed.  
Update packages and install Docker:  
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io docker-compose
```
Don’t forget to add yourself to the `docker` group:  
```bash
sudo usermod -aG docker $USER
```
Log out/in for changes or `newgrp docker` to refresh.
### Step 2: Repository and Config
Pull the Kitchen Board repo:  
```bash
git clone https://github.com/user/kitchen-board.git
cd kitchen-board
```
This step assumes you're comfortable editing `.env` files. Create one in the root directory to tweak settings. The important parts:
```env
PORT=3000
TZ=America/New_York
```
### Step 3: Run the App  
Fire it up with Docker Compose:  
```bash
docker compose up -d
```
Hit `http://<YourLocalIP>:3000` to check if it’s running. If yes, congrats — you’re 90% of the way to ditching Post-its.
### Optional: Auto-start
For always-on setups (like a Raspberry Pi display), enable a restart policy:
```bash
docker update --restart unless-stopped $(docker ps -q --filter "name=kitchen-board")
```
## What Works, What Doesn’t?
At this version (v0.0.18), Kitchen Board is basic and proud of it:
**What’s Good**:
- Simple drag-and-drop widgets: Calendar, grocery lists, family memos. No learning curve.  
- Lightweight: Less than 150MB RAM, tested idling.  
- Local-first: Everything is stored on your machine, not someone else’s server.
**What’s Annoying**:
- No mobile apps: You’re stuck with “go to the IP in your browser.” Not a dealbreaker, but meh.  
- Feature gaps: Things like recurring tasks or deep customization are MIA.  
- Bugs: Expect runtime errors if you play fast and loose with widget settings.
A Reddit user (@diyguru) summed it up well: “It’s good if you don’t push it too hard. Perfect for what it’s trying to be.”
## FAQ
```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Can I run Kitchen Board on a Raspberry Pi?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes! A Raspberry Pi 4 with at least 2GB of RAM should handle Kitchen Board easily. Just don't overload it with other heavy apps."
      }
    },
    {
      "@type": "Question",
      "name": "Does Kitchen Board support Google Calendar sync?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No, Kitchen Board is local-first and doesn't integrate with cloud services like Google Calendar. For advanced syncing, you might need something like Nextcloud or CalDav."
      }
    },
    {
      "@type": "Question",
      "name": "Can I customize the interface?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "To a degree. You can tweak widgets and layouts, but deeper visual changes may require modifying the source code directly. You’ve got the Git repo — go wild."
      }
    }
  ]
}
```
There you go. A functional, minimal wall planner that gives your family a fighting chance at organization without drowning in complexity. Will it stay in the rotation after two weeks? Who knows — but it’s a fun project either way.
