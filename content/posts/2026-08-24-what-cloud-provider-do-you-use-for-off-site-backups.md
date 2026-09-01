---
title: Which Cloud Service for Off-Site Backups? Practical Tips from r/selfhosted
date: '2026-08-24T18:17:53+08:00'
draft: false
tags:
- selfhosted
- cloud
- backups
- linux
summary: We break down r/selfhosted's go-to cloud providers for off-site backups,
  plus real-world setups and costs.
---

Off-site backups are one of those things you don't worry about—until you're crying over corrupted disks and lost data. I'd bet most people in r/selfhosted have been there. Some diehards swear by USB drives buried in the backyard, but let’s be real: cloud storage is easier. Here's how the community keeps their data safe and the trade-offs you should know.
## The Obvious Options: S3-Compatible Buckets
The safest bet is usually S3-compatible storage. It's like the lingua franca of cloud backups. Here are three crowd favorites:
- **Backblaze B2**: Dirt cheap at $5/TB/month. If you’re running a small homelab, you can’t really beat it. It’s directly supported by tools like Rclone, Restic, and Duplicati. One caveat: egress fees. Downloading your data out costs $0.01/GB, so this isn’t ideal if you plan to frequently restore.
- **Wasabi**: About $6/TB/month but no egress fees for typical usage. Think of it as Backblaze with predictable costs—perfect if you’re worried about surprise bandwidth bills. Several users in the thread praised this combo: "Wasabi + Restic is rock solid for me."
- **AWS S3**: Expensive, at around $20.83/TB/month for standard storage, but it’s the gold standard. Rest assured your backups will survive the literal apocalypse (or a heavy Reddit shill war). This is overkill for most home setups unless you’ve got disposable income or free credits.
### The Setup: Backing Up with Rclone + Restic
Just want practical commands? Here's a quick Restic+B2 example to get you started.
1. **Create a B2 bucket**  
   Head to Backblaze, make an account, and create a bucket. Name it something boring like `off-site-backup`.
2. **Install Rclone and Restic**  
   ```
   sudo apt install rclone restic -y
   ```
3. **Create a config file for Rclone** (use `rclone config`)  
   ```
   [b2]
   type = b2
   account = your-account-id
   key = your-application-key
   ```
4. **Initialize Restic to use the bucket**  
   ```
   restic -r rclone:b2:off-site-backup init
   ```
5. **Run your first backup**  
   ```
   restic -r rclone:b2:off-site-backup backup /some/folder --verbose
   ```
A single backup job like this works for occasional snapshots. Schedule it with a cron job if you’re serious:  
```
0 2 * * * restic -r rclone:b2:off-site-backup backup /some/folder
```
Make sure to test restores! A broken backup system might as well not exist.
## Hetzner: The Euro Homelabber’s Darling
Hetzner was name-dropped all over the thread. It’s more like renting your own VPS than pure S3 storage, but it’s cheap (€4.15/month for 1TB with Hetzner Storage Box). The community loves it for its simplicity: SAMBA, SCP, or Rsync—all native and very Linux-friendly.  
Worth noting: Hetzner prices can go up when you factor in server rentals or Snapshots. That said, if you’re already hosting something there, you might as well piggyback your backups.  
For those using Rsync:  
```
rsync -avz /home/user hetzner_user@storage-box.hetzner.com:/backupfolder
```
Just don’t overcomplicate it with containers like one Redditor who “Dockered Rsync” for no reason. (Please don’t be that guy. Rsync stands alone, people.)
## Other Wildcards: Google Drive, iDrive, and… Seafile?
Google Drive might feel out of place in this lineup, but a handful of users vouched for its viability when paired with Rclone. It's free up to 15GB before you're paying $9.99 for 2TB. Just don’t expect any fancy version control—this is jerry-rigging territory.  
One Redditor mentioned iDrive, which offers crazy deals like "$80 for 5TB/year.” The downside? No native self-hosted client support, but you might force it to work with good ol’ Rsync or web uploads.
Then there’s Seafile or Nextcloud, for folks who trust their own setups even for off-site. Caution here: “Off-site” still means somewhere not at your house. Don’t upload to your buddy’s Pi only for his cat to unplug it mid-restore.
## FAQ
### Why not just use Syncthing or Borg?  
Syncthing is more sync than backup—it’s easy to accidentally delete data everywhere. Borg is great but assumes you already have remote storage like a VPS, so it’s just a piece of the puzzle.
### What if I want zero recurring costs?  
Buy a $70 hard drive, encrypt it, and trade off drops with a trusted friend. Slow, awkward, but free.
### Should I encrypt my backups?  
Yes. Use Restic’s built-in encryption or pipe everything through GPG first. Don’t trust raw buckets.  
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why not just use Syncthing or Borg?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Syncthing is more sync than backup—it’s easy to accidentally delete data everywhere. Borg is great but assumes you already have remote storage like a VPS, so it’s just a piece of the puzzle."
      }
    },
    {
      "@type": "Question",
      "name": "What if I want zero recurring costs?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Buy a $70 hard drive, encrypt it, and trade off drops with a trusted friend. Slow, awkward, but free."
      }
    },
    {
      "@type": "Question",
      "name": "Should I encrypt my backups?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. Use Restic’s built-in encryption or pipe everything through GPG first. Don’t trust raw buckets."
      }
    }
  ]
}
</script>
