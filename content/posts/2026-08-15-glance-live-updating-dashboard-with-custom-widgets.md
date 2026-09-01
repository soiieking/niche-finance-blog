---
title: Building a Glance Live-Updating Dashboard with Custom Widgets
date: '2026-08-15T18:00:05+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding Building a Glance Live-Updating Dashboard with Custom Widgets.
---

## Introduction to Glance
Glance is a lightweight, open-source dashboard solution that's perfect for self-hosted setups. I stumbled upon it in a comment from u/linuxlex on r/selfhosted, where they mentioned using it to monitor their Hetzner VPS. What caught my attention was the ease of setting up custom widgets, which I'll dive into – just kidding, let's just get into it.
To get started, you'll need a server with a decent amount of RAM – I'd say at least 2GB, but 4GB or more is recommended. I'm using a Hetzner CX11 plan, which costs around $4/month and has 2GB of RAM. You can also use a DigitalOcean droplet or any other VPS provider. Install Docker and Docker Compose, as we'll be using them to manage our Glance instance.
## Setting Up Glance
First, create a new directory for your Glance setup and create a `docker-compose.yml` file with the following contents:
```yml
version: '3'
services:
  glance:
    image: glance/glance:latest
    ports:
      - "8080:8080"
    restart: always
    environment:
      - GLANCE_DEBUG=true
```
This will pull the latest Glance image and map port 8080 on your host machine to port 8080 in the container. Run `docker-compose up -d` to start the container in detached mode.
## Adding Custom Widgets
Now that Glance is up and running, let's add some custom widgets. I love this tool, but one thing that's still a bit rough around the edges is the widget configuration. You'll need to create a `widgets.json` file in the same directory as your `docker-compose.yml` file. Here's an example with a few basic widgets:
```json
[
  {
    "type": "cpu",
    "title": "CPU Usage"
  },
  {
    "type": "memory",
    "title": "Memory Usage"
  },
  {
    "type": "disk",
    "title": "Disk Usage"
  }
]
```
You can add or modify widgets as needed. One thing to note is that some widgets, like the `cpu` widget, require additional configuration. For example, you can specify a `interval` property to update the widget every 5 seconds:
```json
{
  "type": "cpu",
  "title": "CPU Usage",
  "interval": 5000
}
```
I haven't tested this on ARM, but it should work fine as long as you have a compatible Docker image.
## Dashboard Configuration
With your widgets configured, you can now access your Glance dashboard by navigating to `http://your-server-ip:8080` in your web browser. You'll see a basic dashboard with your custom widgets. You can customize the layout and appearance of your dashboard by editing the `glance.json` file. For example, you can change the theme to a darker theme:
```json
{
  "theme": "dark"
}
```
This is all pretty straightforward, but your mileage may vary depending on your specific setup.
## Performance and Scaling
Glance is designed to be lightweight and efficient, using around 10-20MB of RAM. This makes it perfect for low-end VPS providers like Hetzner or DigitalOcean. However, if you're planning to monitor a large number of servers or add a lot of custom widgets, you may need to consider scaling your setup. One option is to use a load balancer to distribute traffic across multiple Glance instances.
The community is genuinely split on whether to use Docker or Podman for container management. I'm using Docker, but Podman is a great alternative if you're looking for a more lightweight solution.
### FAQ
FAQs are answered in JSON-LD format below:
```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is Glance?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Glance is a lightweight, open-source dashboard solution"
      }
    },
    {
      "@type": "Question",
      "name": "How much RAM does Glance use?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Around 10-20MB of RAM"
      }
    },
    {
      "@type": "Question",
      "name": "Can I use Glance with a load balancer?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, Glance can be used with a load balancer to distribute traffic across multiple instances"
      }
    }
  ]
}
