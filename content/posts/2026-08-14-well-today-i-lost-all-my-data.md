---
title: "I Lost All My Data on a VPS — Here's What Actually Saved Me"
date: 2026-08-14T18:00:45+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "A real r/selfhosted horror story turned into a practical backup guide. Offsite, 3-2-1, and why Docker volumes will betray you."
---

There's a post on r/selfhosted that's been living in my head rent-free. Title: "Well, today I lost all my data." The OP wasn't running some janky Raspberry Pi in a closet. They had a Hetzner CX22, Docker Compose, and a decade of family photos in a bind mount. One `docker volume prune` later — gone. No snapshots. No offsite copy. Just a support ticket to Hetzner that came back with "we can't help."

I've been there. Not with photos, but with a Nextcloud instance I'd spent a weekend tuning. The sick feeling when `ls` returns nothing is something you don't forget.

So let's talk about what actually works, because the thread had some genuinely good advice buried under 200 comments of "should've used ZFS."

## The Three Camps

The community splits into three approaches, and they're not mutually exclusive. But most people pick one and call it a day. That's the mistake.

### Camp 1: The Snapshot Purist

Btrfs or ZFS snapshots, sent to a second disk or a remote host via `zfs send`. This is the gold standard for *recovery speed*. Rolling back a bad update takes seconds, not hours.

The gotcha? Snapshots are not backups. If your house burns down, or your Hetzner box gets wiped by a rogue script, your snapshots die with it. I've seen people run ZFS on a single VPS and call it "backed up." It isn't.

### Camp 2: The Offsite Minimalist

`restic` or `borgbackup` to a Backblaze B2 bucket or a cheap storage VPS. This is what I actually run now. Restic 0.16.0 handles deduplication and encryption out of the box, and the `forget --prune` policy keeps my bucket at about 12 GB for a 40 GB dataset. Costs me $0.60 a month on B2.

Setup time: about 20 minutes if you've never touched it. The killer feature is that it's *offsite*. A `docker volume prune` on the host doesn't touch my B2 bucket.

The downside? Restore speed. Pulling 40 GB back from B2 takes a while. And if you're on a metered connection, that's a bill you'll feel.

### Camp 3: The Managed Cop-Out

Some people in the thread argued for just using a managed service — Hetzner Storage Box, rsync.net, or even a second VPS with `syncthing`. This is fine for *files*, but it falls apart for databases. You can't just rsync a live Postgres data directory and expect a clean restore. You need `pg_dump` or `pg_basebackup` on a schedule.

## The Real Killer: Docker Volumes

Here's the thing nobody says out loud. Docker makes backups *harder*, not easier. Named volumes are opaque. You can't just `tar` them while the container is running and expect consistency. I've lost count of how many people in that thread admitted their "backup" was a `docker cp` of a running container's filesystem.

If you're on Docker, use bind mounts to a known directory like `/opt/containers`. Then at least `restic` can see the raw files. For databases, run a pre-backup hook that dumps to a mounted directory. It's ugly, but it works.

## What I'd Actually Do Tomorrow

If I were starting from zero, with a $5 Hetzner CX22 and a handful of containers:

1. **Bind mounts everywhere.** No named volumes. Ever.
2. **A daily cron job** that runs `pg_dump` for Postgres and `restic backup` for everything else, targeting B2.
3. **A weekly `restic check`** to verify the repo isn't corrupt. This is the step everyone skips, and it's the one that bites you.
4. **A second, manual backup** of the most critical stuff — like your `.env` files and compose YAML — to a private GitHub repo. That's not a backup, but it's a lifesaver when you need to rebuild fast.

The thread's OP could have recovered everything with about 30 minutes of setup. Instead, they're rebuilding from scratch.

## The Honest Truth

I haven't tested this on ARM, and the community is genuinely split on whether `restic` or `borg` is better for large repos. Your mileage may vary. But the core principle isn't up for debate: if your backup lives on the same machine as your data, you don't have a backup.

You have a second copy of the same problem.

## FAQ

### Is a snapshot on the same VPS enough?

No. It protects against accidental deletion and bad updates, but not hardware failure or provider-level issues. You need something offsite.

### How much does offsite backup actually cost?

For a personal setup, expect $1–$3 a month. Backblaze B2 charges $6/TB/month for storage, and egress is free if you use their CDN. Hetzner Storage Box is a flat €3.80/month for 1 TB.

### Can I back up a running Docker container?

You can, but you shouldn't. Use bind mounts and dump databases before backing up. A `docker cp` of a live container is a coin flip for consistency.