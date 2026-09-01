---
title: You Have 100GB of DDR3 RAM Stop Complaining and Build a RAM Disk
date: '2026-08-06T04:00:31+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding You Have 100GB of DDR3 RAM Stop Complaining and Build a RAM Disk.
---

Someone over on r/selfhosted recently posted the ultimate humblebrag: they scored hundreds of gigabytes of DDR3 ECC RAM for practically nothing and had no idea what to do with it. The thread immediately split into two camps: the "just run 400 LXC containers" guys, and the "you don't actually need that much RAM" truth-tellers.
Both are kind of right, but mostly they're missing the fun. 
Let's be real about the hardware landscape. DDR3 is ancient. Unless you're running dual Xeon E5-2680v2 rigs—you know, those 130W TDP space heaters that will literally double your power bill—finding decent motherboards that accept 32GB or 64GB DDR3 DIMMs is a nightmare. one user pointed out that consumer boards from that era cap out at 32GB total because they can't address the high-density 16GB quad-rank sticks. You're basically forced into expensive used server gear. If your electricity costs more than $0.12 per kWh, running an old dual-socket monstrosity 24/7 just to host Nextcloud is financial self-harm. Rent a $14 Hetzner Cloud instance instead.
But let's assume you already have the hardware. You pulled an old Dell R720 out of the e-waste. Now you have 128GB of RAM and a stockpile of SAS drives. What is the actual best use case?
### The "Marginalia" Play: Niche Web Crawling
Stop trying to host 50 redundant Docker instances of apps you barely use just to artificially inflate your RAM utilization. you don't need six instances of Postgres to feel good about yourself. 
Instead, build something that actually requires massive memory. I love the suggestion from the thread about spinning up an Elasticsearch cluster or a massive caching proxy. But my favorite take? Feed it to a web crawler. 
If you want to build your own search engine, like a personal Marginalia clone, you need serious RAM to hold the index. Running a crawler like `crawler4j` or `nodriver` to index a few hundred thousand domains into an Elasticsearch stack will easily chew through 80GB. It gives you a fascinating dataset to query offline.
### ZFS ARC is your new best friend
If search engines aren't your vibe, let ZFS eat the RAM. The Adaptive Replacement Cache (ARC) in OpenZFS 2.2 is notoriously aggressive, which is exactly why I love it. Most people get terrified when they see their RAM utilization sitting at 90%+, but that is just ZFS caching filesystem blocks in memory. 
If you have hundreds of gigabytes of DDR3, designate a 64GB dataset for your boot pool, and let ZFS ARC max out. You can serve Plex media directly off a pool of spinning rust drives, and ZFS will cache all the metadata in RAM. Slow mechanical drives will suddenly hit NVMe-like read speeds for your most frequently accessed files. 
### Give in to the hypervisor lifestyle
Stop using Docker on bare metal for this much RAM. Throw Proxmox on it. Setting up Proxmox takes literally twenty minutes using the standard ISO, and its web GUI is honestly the best in the business. From there, spin up a monster TrueNAS VM with 96GB of RAM attached, and pass through your HBA card via PCIe passthrough. 
I haven't tested this exact passthrough setup on ARM, but the community is genuinely split on whether passing an HBA to a ZFS VM or just running ZFS directly on the hypervisor node is more performant. The Linux kernel is incredibly great at reclaiming memory from the ZFS ARC if the hypervisor itself suddenly needs it, but VM isolation is king if you want to avoid kernel panolas taking down your entire storage array.
Your mileage may vary, but honestly, just having room to spin up a massive Kali Linux VM with 32GB allocated for network testing is a luxury you don't appreciate until you have it. Just don't tell me you're running Pi-hole, AdGuard, Home Assistant, and Vaultwarden in their own dedicated VMs while the system idles at 98% free memory. Consolidate your workloads, build enormous caches, and let the filesystem eat the rest.
