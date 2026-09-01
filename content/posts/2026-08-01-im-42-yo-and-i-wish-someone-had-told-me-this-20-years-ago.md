---
title: 'I''''m 42 and Wish I Knew This 20 Years Ago: The r/sideproject Reality Check'
date: '2026-08-01T18:03:00+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding I''''m 42 and Wish I Knew This 20 Years Ago: The r/sideproject
  Reality Check.'
---

## The $400/Mo Redis Bill Reality Check
A post titled "I'm 42 yo and I wish someone had told me this 20 years ago" popped up on r/sideproject. Usually, these threads devolve into generic hustle-culture garbage or bitter regrets about not buying Bitcoin in 2012. But this one hits different because the guy is entirely right about infrastructure.
His core thesis? We spend an absurd amount of time yak-shaving instead of talking to users. He spent three weeks optimizing a self-hosted Kubernetes cluster to save $20 a month, completely ignoring the fact that his SaaS was bringing in $75. 
I’ve done exactly this. In 2019, I manually provisioned a trio of DigitalOcean droplets with floating IPs to save money on managed load balancers. I spent 40 hours on it. Forty hours to save $18 a month. The math is completely broken, but when you're deep in the indie-hacker trenches, saving that $18 feels like a victory. 
It isn't. It's just procrastination wearing a hard hat. 
## Skip the Hosted DB Trap
Here is where the 42-year-old gets heavily opinionated, and I completely back him up. Stop using AWS RDS for your zero-user side project.
You do not need a highly available Multi-AZ database setup for an app that gets 12 daily visitors. Yet, we do it anyway because "best practices" tell us to. It is total overkill for most people. You can get a Hetzner Cloud instance with 4GB of RAM and 80GB of NVMe storage for €6.59 a month. I didn't test this specifically on ARM yet, but their new T4J series blows anything DigitalOcean or AWS offers out of the water for CPU-to-price ratio. 
If your app actually gets popular, you'll notice before the database melts down. I promise. Just run Postgres directly on the box. 
### Docker vs. Podman: Stop Engineering for FAANG
The thread blew up over containerization. One user insisted that "rootless Podman is the only acceptable answer for a 2024+ deploy." 
I genuinely love Podman. Daemonless containers make sense, and you don't have to worry about a rogue container wiping your root filesystem. But it has one fatal flaw: the dev experience still trails Docker, and you will burn two days troubleshooting why your pod can't resolve internal DNS on a Hetzner VPS. 
The community is genuinely split. Half of r/sideproject swears by raw Go binaries deployed via SSH, the other half writes 800-line docker-compose files for static landing pages. If you can't ship your MVP in a single 50MB Go binary that you just SFTP to a $4 Linux box, you're overcomplicating it. I haven't slept in three days, but my deploy script is literally a 12-line bash file. 
## Stop Writing Code, Start Talking to People
At 42, the original poster realized the biggest waste of his twenties and thirties wasn't writing bad code—we all do that—but writing irrelevant code. He built a tool to track his own fitness metrics, then spent six weeks making the rating algorithm incredibly fast before he ever asked another human if they'd pay for it.
Your mileage may vary, but I bet it doesn't. It took me years to grasp that PHP from 2004 and MySQL deployed on a shared Dreamhost server would have been perfectly fine for a project that never topped 100 users. 
We don't build side projects to scale to a million concurrent users. We build them to prove someone actually wants the thing. Pick a box. Install Postgres 16. Get it live. If your architectural decisions can survive the harsh reality of absolute silence after you launch, you're on the right track.
