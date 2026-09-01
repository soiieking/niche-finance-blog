---
title: I Am the Guy Who Lost All the Data the Other Week
date: '2026-08-27T14:00:33+08:00'
draft: false
tags:
- selfhosted
- backup
- disaster-recovery
- linux
summary: What losing all my self-hosted data taught me, and how you can prevent the
  same nasty lesson.
---

## I Screwed Up, and I Know It
Let me say it upfront: it was my fault. I was the guy who lost all the data the other week. My server, my configs, every lovingly self-hosted app—gone. All because I got cocky and skipped the boring stuff. 
Backups? Half-working at best. A restore plan? Ha! I was that person in every r/selfhosted thread asking, “Do I really need RAID on a personal media server?”
Spoiler: yes, or at least something like it.  
Here’s how I nuked my setup, what I would’ve done differently, and why it took a total failure for me to finally get my priorities straight.
## The Set-Up (and the Inevitable Screw-Up)
I thought I had a respectable home server game going. A Proxmox box running on an old ThinkPad X230 (yeah, I know, call me cheap, but this thing sips power). Docker containers were humming along with Jellyfin, HomeAssistant, and even a small Paperless-ngx instance.
Storage? A single 4TB WD Red slapped into a USB 3 dock. It wasn’t elegant, but it worked—and *it was backed up… sort of.*
I had scheduled daily rsync jobs. They were supposed to copy data to another tiny VPS on Hetzner that I used as a cold backup (storage costs: €3/month, bless Hetzner pricing). But here’s the thing: I never checked those jobs. Not once. For all I know, they’ve been silently failing for the past six months.
Then, disaster struck. My 4TB drive flaked out—click-click-click and dead. I dug up some dusty, outdated tarball backups… but yeah, half my data was gone forever. The rest was a disorganized mess of partial restores. All I could do was pour a stiff drink and start rebuilding from scratch.
## Where I Went Wrong
Let’s break this down because, honestly, most of my setup wasn’t even terrible. It was just incomplete. 
### Mistake #1: Trusting a Backup Plan I Never Tested  
Here’s my big PSA: **a backup isn’t a backup unless you’ve tested the restore.** Don’t just set up something like Borg and assume it’s bulletproof. You gotta simulate a failure and actually pull files from your archive.
r/selfhosted regulars talk about this all the time, and I always dismissed it. “I’m not an enterprise sysadmin,” I’d tell myself. But guess what? Even if you’re just babysitting Plex and Radarr, data loss *still sucks.*
### Mistake #2: No Redundancy  
That single external drive setup? Totally preventable. I’m not saying everyone needs fancy ZFS replication or a Ceph cluster (overkill for most home users), but at least mirror backups between two drives or cloud services. Use rclone to sync important folders to Backblaze B2 or Wasabi—something cheap but reliable for long-term storage.
If you’re running something critical like HomeAssistant, full-disk backups are king. Clonezilla would’ve saved me two days of reconfiguring that nightmare.
### Mistake #3: Ignoring SMART Data  
Can we talk about SMART monitoring? Because I didn’t. One periodic `smartctl -a` on my drive could’ve warned me about its slow death march *weeks ago.* My drive was running hot (40+°C during heavy loads because USB docks suck for cooling), and I just shrugged. Proxmox has built-in ZFS and SMART health reporting. I could’ve set up alerts—but nope. Too lazy.
## What I’m Doing Now (to Avoid Screwing Up Again)
Fixing this mess has been a humbling but educational process. If you’re reading this and crossing your fingers that your setup’s “probably fine”… learn from my pain.
1. **Backups, Tested and Logged:**
   I rebuilt my rsync scripts but added nightly logging to Slack using `mailx`. On top of that, I’m experimenting with BorgBase (free for small-scale users) for encrypted offsite backups. 
2. **Drive Redundancy:**
   Replaced the USB dock with a cheap Synology DS220j. Barebones, but it runs RAID1, consumes just ~15W idle, and has a real DSM backup wizard.
3. **Automation/Monitoring:**
   SMART alerts now ping me via Telegram every week. Free tools like smartmontools and Node Exporter (with Grafana) make this easy if you care even a little.
4. **Configuration Backups to Git:**
   Learned this trick from a Reddit thread: all my Docker Compose/YAML files now live in a private Bitbucket repo. Easy to pull and rebuild in an emergency.
## The Takeaway (AKA: Just Don’t Be Me)
Look, none of this is groundbreaking. If anything, it’s the basics: backup, monitor, repeat. But there’s a reason you keep hearing this advice over and over on r/selfhosted. Because losing data once—*even your “unimportant” Plex media—is all it takes to scare you straight.
Don’t wait for a catastrophe to become the guy who lost all his data. Test your backups today. Set up those drive alerts. Spend a Saturday solving problems *before* they become existential crises. I now know better—and trust me, it’s a lot less painful to intervene early.
