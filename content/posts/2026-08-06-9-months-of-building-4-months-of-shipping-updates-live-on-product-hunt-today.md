---
title: "9 Months to Build, 4 Months Tinkering: The Real Cost of Indie Dev Alone"
date: 2026-08-06T00:00:30+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "A reality check on the r/sideproject playbook of taking nearly a year to launch, and the brutal infrastructure choices that follow."
---

Saw a post on r/sideproject today that made my eye twitch. A solo dev finally launching on Product Hunt after spending 9 months building and 4 months shipping minor updates. That’s 13 months of pure momentum-killing isolation. 

I get it. You want the architecture to be flawless. But 13 months before a real launch isn't a milestone—it's a hostage situation. You are building in a vacuum, guessing what users want, and convincing yourself that adding another Redis layer is a substitute for customer feedback. 

Stop doing this. Ship the ugly version. The architecture doesn't matter if nobody is paying you. Once you do have users, here is the actual reality of the infrastructure choices waiting for you.

### The VPS vs. Serverless Smackdown

Most devs in that thread were agonizing over their hosting stack. A few commenters were proudly defending their AWS ECS setups. Running ECS as a solo dev for a side project is absolute overkill for most people. You are spending 15 hours a month managing IAM roles just to save $4 on compute. 

The VPS route is still king. But skip DigitalOcean. A basic 2GB DigitalOcean droplet runs $24 a month and gives you a single vCPU. Hetzner offers an ARM-powered CAX11 with 4GB RAM and 2 vCPUs for €3.79 (~$4.15) a month. If you're terrified of ARM compatibility, I haven't tested my Rust binaries on Hetzner's ARM instances yet, but the community is genuinely split—some swear it's flawless, others complain about weird segfaults when libc versions mismatch. Your mileage may vary.

For pure web workloads, grab an x86 instance from Hetzner or OVH, install Dokku, and move on with your life. 

### Containerization: Docker vs. Podman

If you're not deploying bare metal with бинарники (binaries), you need containers. Docker is the default, and Docker Swarm is remarkably underrated for indie hackers. People mock Swarm because Kubernetes won the enterprise war, but for a solo dev with 3 microservices, K8s is a massive overhead trap. 

I love Docker Desktop on my Mac, but it has one fatal flaw: it eats 8GB of RAM on boot just to sit idle. I recently switched my local dev environment to Podman. Podman is daemonless, runs rootless by default, and dramatically lighter on system resources. The catch? Podman occasionally chokes on complex `docker-compose` files that use `depends_on` with complex health conditions. It’s profoundly annoying. 

If your compose file is simple, use Podman. If you have a tangled web of service dependencies, stick with Docker. 

### Database Delusions

The biggest unseen bug in the thread was the OP's database choice. For a solo launch, Postgres is the only correct answer. Everyone rushing to use MongoDB because they read it scales better needs a reality check. You won't hit 10 million users in month one. 

Self-hosting Postgres on a $4 VPS is a fool's errand. You won't set up S3 backups properly, and when your node crashes, you will cry. Just use Neon or Supabase. Yes, managed Postgres is pricier. Neon’s free tier pauses your compute after a week of inactivity, which is brutal for an app that needs to be cold-started instantly by a webhook. Supabase gives you 500MB of storage for zero dollars, but upgrading to their Pro tier costs $25 a month. It's a steal compared to AWS RDS, which will quietly bleed you dry with hidden IOPS charges. 

Skip the 13-month launch cycle. Skip the Kubernetes. Skip the domestic cloud providers. Get a cheap Hetzner box, throw Postgres on Supabase, and put your app in front of real humans before the code rots.