---
title: 'BackupBeforeYouFKUP: Why RAID Isn''''t a Backup and Your Homelab is Cooking'
date: '2026-08-06T10:00:33+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding BackupBeforeYouFKUP: Why RAID Isn''''t a Backup and Your Homelab
  is Cooking.'
---

We all know that guy on r/selfhosted. The one with the 8-bay TrueNAS server bristling with 18TB drives, three Docker swarms, and zero off-site backups. 
He posts a frantic update on Tuesday afternoon: "Proxmox host died, ZFS pool locked, lost ten years of family photos."
Man. I feel for him, but come on. We've all built and broken things, and the one lesson you learn after spending 12 hours trying to rebuild a corrupted Postgres database at 3 AM is that RAID is not a backup. RAID protects you from a dead drive. It doesn't protect you from fat-fingering an `rm -rf` in the wrong directory, a ransomware payload, or your cat chewing through the power cable and frying the PSU.
You need an off-site backup. 
## Read-only is your best friend
When that r/selfhosted thread popped up about the BackupBeforeYouFKUP mantra, the best comment wasn't some complex Kubernetes CronJob scheduling trick. It was a guy talking about the 3-2-1 rule: three copies of your data, on two different media, with one off-site. The real kicker? His off-site was a $4.50/month Hetzner Storage Box mounted via SSHFS that only ran as a read-only mount by default. 
Unmount, mount read-write, sync, unmount. That limits blast radius. If a cryptolocker hits your local network, it can't easily encrypt your off-site archive if the mount isn't live.
I love this. It’s simple, brute-force, and ugly. I run my own off-site syncs via BorgBackup on a Hetzner Box, and it took me about 9 minutes to set up the systemd timer. Borg handles compression and deduplication, meaning my bloated 120GB Nextcloud instance only pushes about 2GB of actual new block-level changes every night. Storage Boxes cost like €3.89 a month for 100GB, or €12.89 for 1TB. 
Forget Backblaze B2 for this. Don't get me wrong, B2 is great. But comparing Hetzner's flat-rate Storage Box to B2's $0.006/GB/month egress and storage costs is a no-brainer for budget-obsessed homelabbers. B2 adds up fast when you're pushing terabytes of media. 
## Automation and Air Gaps
I tried backing up to an external USB drive once. I kept it plugged into my main server. That lasted exactly two weeks before I forgot to plug it back in after a reboot. 
Human discipline is a terrible backup strategy. You need automation. 
I use Restic now for my Docker volumes. It creates encrypted, deduplicated repositories that you just blast over SFTP to any remote VPS or storage provider. You have no idea how liberating it is to just point Restic at a randomized Hetzner VPS running Alpine Linux for €4/month, type in your password, and let it spin. 
Still, the community is genuinely split on how to implement this. Some guys tell you to pull a 10TB external drive out of a fireproof safe, plug it in, run `rsync`, and put it back.Air-gapped is bulletproof. It physically can't be touched by network malware. But I haven't tested this on ARM, and honestly, I just can't be bothered to do manual disk-swaps every Sunday. If you can stomach the routine, do it. If you're lazy, automate it. But that automation should run as a completely separate user with minimal privileges.
## attack surface
I once spent a weekend building a convoluted sync pipeline that pushed from local -> VPS -> Wasabi -> AWS glacier. Total overkill for most people, including myself. I tore it down. 
If you have your primary data, local backups on a separate machine, and an off-site sync running every 24 hours, you're already in the top 5% of homelabbers. 
The goal isn't to survive a direct nuclear strike on your data center. It's just to survive your own stupid mistakes. Keep the rotation tight. Test your restores. And do it before you FKUP.
