---
title: 'Dockhand vs. Restic vs. BorgBackup: The Ultimate Self-Hosted Docker Backup
  Guide'
date: '2026-07-26T09:22:44+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Dockhand vs. Restic vs. BorgBackup: The Ultimate Self-Hosted
  Docker Backup Guide.'
---

## The Community Spark: Solving the Docker Backup Dilemma
A recent trending thread on Reddit's `r/selfhosted` community reignited a fierce debate: "What is the best way to backup Docker containers and their volumes?" As homelabs and self-hosted VPS environments grow more complex, users are moving away from simple `tar` cron jobs toward purpose-built solutions like **Dockhand**. The community spark behind this trend is simple: traditional backup tools often fail to capture the ephemeral nature of Docker volumes, leading to corrupted states during restoration. 
## Synthesized Community Perspectives
The `r/selfhosted` consensus highlights that a good Docker backup strategy must handle three things: volume persistence, database consistency, and off-site redundancy. 
*   **The Dockhand Advocates:** Power users praised Dockhand for its Docker-native design. It automatically pauses or safely dumps databases before snapshotting volumes.
*   **The Restic Loyalists:** A large segment argued that while Dockhand is great for local orchestration, **Restic** remains undefeated for fast, encrypted, incremental off-site backups to S3-compatible storage.
*   **The BorgBackup Traditionalists:** Veterans advocated for **BorgBackup**, citing its battle-tested deduplication and reliability, despite requiring wrapper scripts to interact with Docker volumes.
The core debate boiled down to convenience versus absolute control. The community agreed that whichever tool you choose, it must perform application-consistent backups, not just flat file copies of live databases.
## Deep-Dive Actionable Guide: Setting Up a Dockhand Backup Pipeline
Based on community-tested configurations, here is a practical guide to implementing a robust Docker backup system using Dockhand for local snapshots and Restic for off-site redundancy.
### Step 1: Prepare the Backup Directory and Network
Create a dedicated backup volume and a Docker network to allow your backup container to interact with your app containers.
```bash
docker volume create backup_data
docker network create backup_net
```
### Step 2: Deploy a Dockhand-Compatible Backup Container
While Dockhand orchestrates the backup process, you can use a community-favored Docker Compose setup to handle volume syncing. Here is a synthesized, reliable configuration:
```yaml
version: '3.8'
services:
  backup:
    image: dockhand/backup:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - backup_data:/backup
      - app_data:/data/app:ro
    environment:
      - BACKUP_CRON=0 2 * * *
      - RESTIC_REPOSITORY=s3:https://s3.amazonaws.com/my-backup-bucket
      - RESTIC_PASSWORD=supersecret
      - AWS_ACCESS_KEY_ID=your_key
      - AWS_SECRET_ACCESS_KEY=your_secret
volumes:
  app_data:
  backup_data:
```
### Step 3: Pre-Backup and Post-Backup Hooks
To ensure database consistency, the community heavily recommends using Docker labels in your app containers to execute safe dump commands before backups run. 
Add these labels to your database container (e.g., PostgreSQL):
```yaml
labels:
  - "dockhand.backup.pre=true"
  - "dockhand.backup.pre.command=pg_dump -U \$POSTGRES_USER -Fc \$POSTGRES_DB > /backups/db.dump"
  - "dockhand.backup.post=true"
  - "dockhand.backup.post.command=rm /backups/db.dump"
```
When the backup container spins up or triggers a cron job, it reads the Docker socket, executes the `pre` command to dump the database to a volume, safely copies that volume, and then runs the `post` command to clean up.
## Pros & Cons / Comparative Table
Based on the Reddit thread, here is how the top solutions stack up for self-hosted VPS deployments:
| Tool | Best Feature | Docker Integration | Off-site Capability | Complexity |
| :--- | :--- | :--- | :--- | :--- |
| **Dockhand** | Docker-native orchestration | Excellent (reads socket, uses labels) | Requires Restic/Borg integration | Medium |
| **Restic** | Fast, encrypted incremental backups | Poor (requires external scripts to pause containers) | Excellent (S3, B2, SFTP native) | Medium-High |
| **BorgBackup** | Advanced deduplication & stability | Poor (requires wrapper scripts) | Good (via Borgmatic or SFTP) | High |
## The Verdict / Expert Advice
The `r/selfhosted` community proved there is no single "silver bullet," but the optimal setup depends on your infrastructure:
1.  **The Homelabber / Beginner:** Start with **Dockhand**. Its label-based system and Docker socket integration make it incredibly easy to ensure your containers are backed up safely without writing complex bash scripts.
2.  **The VPS Power User:** Use **Dockhand for local snapshots and database dumps**, but pipe that output directly into **Restic** for encrypted, incremental push to an S3 bucket. This combines Dockhand's container awareness with Restic's unmatched cloud efficiency.
3.  **The Bare-Metal Veteran:** If you are already running Proxmox or a ZFS pool, leverage filesystem-level snapshots, and use **BorgBackup** to push those compressed snapshots off-site. 
## Frequently Asked Questions (FAQ)
**Does Dockhand support live database backups?**
Yes. By utilizing Docker labels for pre-backup and post-backup commands, Dockhand can trigger tools like `pg_dump` or `mysqldump` to create a consistent snapshot before copying the files, preventing database corruption.
**Is Restic better than BorgBackup for off-site storage?**
Restic is generally preferred for cloud backups due to its native support for S3-compatible storage and Backblaze B2. BorgBackup is highly efficient for VPS-to-VPS backups over SSH/SFTP but lacks native cloud storage APIs.
**How often should I backup my self-hosted Docker volumes?**
The community consensus is to follow the 3-2-1 backup rule. For most homelabs, a daily cron job (e.g., 2:00 AM) is sufficient. However, for high-write applications like Nextcloud, you should implement continuous replication or twice-daily backups.
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Does Dockhand support live database backups?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. By utilizing Docker labels for pre-backup and post-backup commands, Dockhand can trigger tools like pg_dump or mysqldump to create a consistent snapshot before copying the files, preventing database corruption."
      }
    },
    {
      "@type": "Question",
      "name": "Is Restic better than BorgBackup for off-site storage?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Restic is generally preferred for cloud backups due to its native support for S3-compatible storage and Backblaze B2. BorgBackup is highly efficient for VPS-to-VPS backups over SSH/SFTP but lacks native cloud storage APIs."
      }
    },
    {
      "@type": "Question",
      "name": "How often should I backup my self-hosted Docker volumes?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "The community consensus is to follow the 3-2-1 backup rule. For most homelabs, a daily cron job (e.g., 2:00 AM) is sufficient. However, for high-write applications like Nextcloud, you should implement continuous replication or twice-daily backups."
      }
    }
  ]
}
</script>
