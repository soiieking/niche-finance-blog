---
title: Getting Your First Paid App From Localhost to the Real Internet
date: '2026-08-02T02:10:01+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding Getting Your First Paid App From Localhost to the Real Internet.
---

A stranger bought my first paid app today. After a year of building, it finally feels real.
I saw that exact phrase on r/sideproject this morning. The OP was freaking out because someone they didn't know actually dropped $20 on their tool. It’s a wild feeling. You spend months building in the dark, convinced your UI is garbage, and then a Stripe webhook hits your server. 
But getting that webhook to actually fire? That's where half of r/sideproject gets stuck. People spin up an EC2 instance, leave port 22 open to 0.0.0.0/0, and wonder why their server crashes. Here is how I ship side projects and actually get paid, using the exact tools I've broken and rebuilt over the last year.
## Skip AWS. Get a Bare Metal Box.
I love AWS, but it is complete raging overkill for most people. You do not need an Application Load Balancer and a VPC with NAT Gateways for a $20/month SaaS. You will burn $30 a month before you even get a single user. 
Go to Hetzner. Spin up a CX22 instance. It costs about $4.50 a month and gives you 2 AMD vCPUs and 4GB of RAM. That is more than enough to run a Postgres database and your app. 
DigitalOcean is fine too, but you're paying a 3x premium for a slick UI. I haven't tested Hetzner's ARM instances for Node yet, so your mileage may vary there, but x86 is rock solid. 
## Use Docker. Just Do It.
I tried going raw-dog with PM2 and Node for a while. The community is genuinely split on whether PM2 or Docker is better for solo deployments, but PM2 relies on your host OS having the exact right dependencies. It is a nightmare when you inevitably need to switch servers.
Wrap your app in a Docker container. It reduces your entire deployment strategy to moving a tarball. 
Here is the real `docker-compose.yml` I use to put a web app behind Cloudflare:
```yaml
version: '3.8'
services:
  app:
    image: ghcr.io/yourusername/your-app:latest
    restart: unless-stopped
    env_file:
      - .env
    ports:
      - "127.0.0.1:8080:8080"
  caddy:
    image: caddy:2.7-alpine
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
      - caddy_data:/data
    depends_on:
      - app
volumes:
  caddy_data:
```
Notice the `127.0.0.1:8080` mapping. This binds the app port to localhost only. The outside world can only touch your app through Caddy. 
I use Caddy over Nginx for one reason: automatic HTTPS. Caddy handles the Let's Encrypt cert provisioning and renewal out of the box. You do not touch certbot. You do not write cron jobs. 
A barebones `Caddyfile` looks like this:
```text
yourdomain.com {
    reverse_proxy localhost:8080
}
```
That's it. Caddy listens on 80 and 443, figures out your SSL cert, and proxies traffic to your app. 
### The Stripe Webhook Gotcha
If you're using Stripe to process that first magical $20 payment, you need to test your webhooks locally before deploying. 
The syntax in the Stripe CLI changes constantly. As of version 1.18.0, this is the exact command that works without throwing deprecated warnings:
```bash
stripe listen --forward-to localhost:8080/webhooks/stripe
```
Run that in a separate terminal window. Fire a test event from your Stripe dashboard. If your server logs don't react immediately, check your CSRF middleware. I spent four hours last week debugging a silent 403 on my webhook endpoint because my Express middleware was eating the POST request.
## Just Bleeping Ship It
You can tweak your landing page forever. You can obsess over your font weights. 
But none of it is real until a stranger buys it. Spin up the $4 box, write the 4-line Caddyfile, and get your app on the public internet. 
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [{
    "@type": "Question",
    "name": "Is Hetzner or DigitalOcean better for launching a side project?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Hetzner is significantly cheaper for the same compute resources, costing around $4.50 for a 2 vCPU, 4GB RAM instance. DigitalOcean is roughly three times the price but offers a more user-friendly interface. For solo devs trying to minimize burn rate, Hetzner is the better choice."
    }
  }, {
    "@type": "Question",
    "name": "Why use Caddy over Nginx for solo indie hacker projects?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Caddy automatically provisions and renews Let's Encrypt SSL certificates by default. With Nginx, you have to configure and manage certbot manually. Caddy reduces your infrastructure setup to a few lines in a Caddyfile, which is ideal when you want to ship fast."
    }
  }, {
    "@type": "Question",
    "name": "Why is my Stripe webhook failing silently in production?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "The most common cause of a silent Stripe webhook failure is your app's CSRF middleware blocking the incoming POST request. Ensure your webhook endpoint is excluded from CSRF protection and that you are validating the Stripe signature header correctly."
    }
  }]
}
</script>
