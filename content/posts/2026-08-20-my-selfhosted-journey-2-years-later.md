---
title: "My SelfHosted Journey - 2 Years Later"
date: 2026-08-20T18:00:29+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "A no-nonsense look at the highs and lows of running a self-hosted setup for two years. Spoiler: it's been a wild ride."
---

## The Journey Begins

I started my self-hosted journey about two years ago, and I've learned a thing or two about what works and what doesn't. I was initially drawn to the idea of having full control over my online presence, and I was willing to put in the work to make it happen.

I started with a basic setup using Ubuntu 20.04 on a Hetzner VPS (CPX21). I chose Hetzner because of their excellent support and competitive pricing – I was able to get a 1GB RAM, 2-core CPU instance for $10/month. I won't lie, it was a bit of a pain to set up, but I managed to get everything up and running in about 2 hours.

### The Early Days

One of the biggest challenges I faced was getting my head around the concept of "server administration." I mean, I'd used Linux before, but running a server is a whole different ball game. I spent hours poring over tutorials and documentation, trying to get everything configured just right. I remember one particularly frustrating evening where I spent 3 hours trying to troubleshoot a DNS issue – I finally figured it out by posting a desperate plea on the r/selfhosted subreddit, and the community came through with a solution in minutes.

## The Good, the Bad, and the Ugly

Fast forward two years, and my setup has evolved significantly. I've moved to a more modern setup using Docker 20.10 on a Hetzner CX51 instance (4GB RAM, 4-core CPU, $20/month). I love Docker because it makes it so easy to manage and scale my services – I can spin up a new container in seconds, and it's been a game-changer for my workflow.

However, I've also had my fair share of issues. One of the biggest problems I've faced is dealing with the community split on Docker vs. Podman. Some people swear by Podman, citing its improved performance and security features. Others, like u/xenopeek, argue that Docker is still the better choice due to its wider adoption and more extensive ecosystem. I won't take a side here, but I will say that I've had some issues with Docker's resource usage – on a particularly busy day, I saw my instance's RAM usage spike to 90% due to a runaway container.

### The "I Love It, But..." Moment

One of the tools I've fallen in love with is Nextcloud 23. I use it to store all my files, and I've even set up a custom theme to make it look like a mini-website. However, I have to admit that I've had some issues with its performance on my current setup. I've tried tweaking the configuration, but it's been a bit of a challenge to get it running smoothly. I've even resorted to using a separate instance just for Nextcloud, which is a bit overkill if you ask me.

## The Future is Now

Looking back on the past two years, I'm struck by how far I've come. I've learned so much about server administration, and I've developed a real appreciation for the power of self-hosting. Of course, there are still plenty of challenges ahead – I'm planning to migrate to a new instance with more resources, and I'm excited to see how things will change.

If you're thinking of joining the self-hosted community, I say go for it. It's not always easy, but the rewards are well worth it. Just be prepared to put in the work, and don't be afraid to ask for help when you need it.

---

FAQ
-----

### Q: What's the best VPS provider for self-hosting?

A: I've had good experiences with Hetzner, but DigitalOcean is also a popular choice. Ultimately, it depends on your specific needs and budget.

### Q: Is Docker still the best choice for self-hosting?

A: The community is split on this one. Some people swear by Docker, while others prefer Podman. I'll say that Docker has worked well for me, but your mileage may vary.

### Q: How do I optimize my self-hosted setup for performance?

A: This is a tough one, and it really depends on your specific setup and needs. However, I'll say that I've found that tweaking the configuration and using a separate instance for resource-intensive services can help improve performance.