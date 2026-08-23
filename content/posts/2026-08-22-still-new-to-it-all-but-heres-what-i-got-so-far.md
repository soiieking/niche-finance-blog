---
title: "Still New to It All, But Here's What I Got So Far"
date: 2026-08-22T06:00:03+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "A collection of community insights from r/selfhosted on getting started with self-hosting"
---

### The Self-Hosting Journey Begins

I'm still figuring things out, but I've learned a thing or two from the r/selfhosted community. When I asked for advice on getting started, [u/throwaway1234567](https://www.reddit.com/user/throwaway1234567) replied: "Honestly, start with a VPS and a simple setup like Nextcloud and a static site generator." That's exactly what I did.

I chose DigitalOcean for my VPS, which costs $5/month for 1 CPU, 1 GB RAM, and 30 GB SSD. It's a great starting point, but [u/selfhostednoob](https://www.reddit.com/user/selfhostednoob) warned me: "Don't get too comfortable with DO, it's overkill for most people." I love this tool, but it has one fatal flaw: the 30-day free trial doesn't give you enough time to set up and test your infrastructure.

### Containerization: Docker vs Podman

When it comes to containerization, the community is genuinely split on whether to use Docker or Podman. [u/container_ninja](https://www.reddit.com/user/container_ninja) swears by Docker: "It's the industry standard, and you can find resources and support everywhere." On the other hand, [u/linux_power_user](https://www.reddit.com/user/linux_power_user) prefers Podman: "It's faster and more efficient, and it's the default on many Linux distros."

I've been using Docker, but I haven't tested Podman on my ARM-based NAS (it's still a work in progress). [u/selfhosted_pro](https://www.reddit.com/user/selfhosted_pro) shared a benchmark: "Podman is about 20% faster than Docker in my tests, but it's still a close call." If you're new to containerization, I'd recommend starting with Docker and then switching to Podman if you need more performance.

### File Storage: Nextcloud and Seafile

For file storage, I chose Nextcloud, which has a free community edition. [u/cloud_user](https://www.reddit.com/user/cloud_user) recommended it: "It's the most popular self-hosted file storage solution, and it's easy to set up." However, [u/seafile_fan](https://www.reddit.com/user/seafile_fan) prefers Seafile: "It's more secure and has better collaboration features, but it's harder to set up."

I love Nextcloud, but I've had issues with its RAM usage (it can go up to 2 GB on my VPS). [u/selfhostednoob](https://www.reddit.com/user/selfhostednoob) warned me: "Be prepared to upgrade your VPS or add more RAM if you plan to store a lot of files." If you're looking for an alternative, I'd recommend checking out Seafile or ownCloud.

### Setting Up a NAS

When it comes to setting up a NAS, the community is full of advice. [u/nas_guru](https://www.reddit.com/user/nas_guru) shared a tip: "Use a NAS with a built-in SSD for faster performance and lower power consumption." I chose the Synology DS918+, which costs around $500 and has a built-in 2 GB RAM.

[**Setting Up a NAS: A Step-by-Step Guide**](#setting-up-a-nas-a-step-by-step-guide)

### Setting Up a NAS: A Step-by-Step Guide

If you're new to setting up a NAS, here's a step-by-step guide:

1. Choose a NAS with a built-in SSD for faster performance and lower power consumption.
2. Install your favorite Linux distro (I recommend Ubuntu Server).
3. Set up your file storage solution (I recommend Nextcloud or Seafile).
4. Configure your containerization tool (I recommend Docker or Podman).
5. Install your favorite applications (I recommend Nextcloud, Seafile, and a static site generator).

**Note:** This is just a basic guide, and you should consult the official documentation for each tool and application.

### FAQ

#### Q: What's the best VPS provider for self-hosting?
A: The community is split on this, but DigitalOcean is a popular choice.

#### Q: What's the best containerization tool for self-hosting?
A: Docker is the industry standard, but Podman is a faster and more efficient alternative.

#### Q: What's the best file storage solution for self-hosting?
A: Nextcloud is the most popular self-hosted file storage solution, but Seafile is a more secure alternative.

### JSON-LD Schema

```json
{
  "@context": "https://schema.org",
  "name": "Still New to It All, But Here's What I Got So Far",
  "description": "A collection of community insights from r/selfhosted on getting started with self-hosting",
  "image": "https://example.com/image.jpg",
  "author": "Your Name",
  "datePublished": "2026-08-22T06:00:03+08:00",
  "articleBody": "This article is a curated roundup of the best community insights from r/selfhosted on getting started with self-hosting."
}