---
title: "Petition to Ban 'I Built' in r/selfhosted Titles: A Better Way to Show Off"
date: 2026-08-05T00:00:25+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Stop writing 'I built a self-hosted X' on r/selfhosted. Here is the right way to share your project, write your GitHub README, and deploy."
---

We get it. You spent three weekends without sunlight wiring together a Docker compose file and now you want the dopamine hit of upvotes. But the r/selfhosted community is practically begging for a moratorium on "I built" post titles. 

When every other post is "I built a Google Photos alternative" or "I built a dashboard for my homelab," the titles lose all meaning. Half the time it’s just a slightly modified Nginx config running on a $4 Hetzner CX22. The other half, it is a genuinely brilliant tool that gets buried because nobody knows what it actually does.

Let’s fix how you share your projects. 

### Skip the Ego, Name the Stack

If you actually want people to use your tool or appreciate your architecture, tell them what it is and what it runs on. A comment from u/throwaway_homelab in that thread put it perfectly: *"I built a Rust-based media aggregator that uses 20MB of RAM, here is the docker image."* 

That is a good title. It gives me the stack, the resource footprint, and the deliverable. I know immediately if I care. If your project requires 2GB of RAM and a Postgres database just to track my grocery list, I am scrolling past. Be specific.

### Dockerize or Die

If you post a project to r/selfhosted without a container, you will get roasted. I learned this the hard way back in 2022 when I dropped a bare-metal Python script that expected a global systemd install. 

Do not just slap a `Dockerfile` together and call it a day. You need a `docker-compose.yml` that actually works without the user having to mount seventeen different volumes. 

Here is a baseline compose file that won't make self-hosters hate you:

```yaml
version: "3.8"
services:
  your_app:
    image: ghcr.io/youruser/your_app:latest
    container_name: your_app
    environment:
      - DATABASE_URL=sqlite:////data/app.db
      - PORT=8080
    volumes:
      - ./data:/data
    ports:
      - "8080:8080"
    restart: unless-stopped
```

This setup keeps the state persistent, uses SQLite so people aren't forced to spin up a separate MariaDB container, and uses `unless-stopped` so it survives a reboot. 

### Write a README for Tired Sysadmins

Your GitHub README should not be a philosophical essay about why you built the app. I haven't tested this on every demographic, but developers do not care about your origin story. They care about two things: how much RAM it eats and how to install it.

Give me a one-liner curl install script or a copy-paste Docker command. 

`docker run -d -p 8080:8080 -v ./data:/data ghcr.io/youruser/your_app:latest`

If your install instructions require me to manually compiling a Go binary, compiling Node modules, and hunting down missing C-libraries, your project is not ready. Podman users will also hate you if your image requires raw Docker socket access without a massive warning. Keep it simple.

### Deal with the Hacker News Hug of Death

So you wrote a decent title, posted the compose file, and actually followed the rules. Now r/selfhosted is sending 500 unique visitors to your $5 DigitalOcean droplet. 

Your server will immediately die. 

DigitalOcean gives you 1TB of outbound bandwidth, but your 1GB RAM droplet will choke on the PHP processes long before you hit that cap. If you are hosting a web demo alongside your repo, put it behind Cloudflare. Yes, Cloudflare goes down sometimes, and yes, the community is genuinely split on whether Cloudflare breaks the spirit of self-hosting. But for a demo link in a Reddit post, it is a no-brainer. 

Alternatively, host the demo on a Hetzner Cloud instance. You get four times the RAM for the same price as DO, which buys you enough overhead to survive the initial Reddit traffic spike.

Stop saying "I built." Tell us what it is, show us the RAM usage, and give us a working container. Then you can have your upvotes.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why does r/selfhosted hate 'I built' in post titles?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "The phrase is overused and clickbaity. Users prefer titles that state exactly what the software does, the tech stack used, and the resource requirements, rather than hiding the actual project behind a generic 'I built' hook."
      }
    },
    {
      "@type": "Question",
      "name": "Do I need Docker to post a self-hosted project?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "While technically you can post bare-metal install guides, the community strongly prefers projects packaged in Docker or Podman containers. Providing a docker-compose.yml ensures your project is easy to test and deploy without breaking the user's existing system dependencies."
      }
    }
  ]
}
</script>