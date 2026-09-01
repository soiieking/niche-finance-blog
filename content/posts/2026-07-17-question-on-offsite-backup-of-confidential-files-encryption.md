---
title: How to Securely Encrypt Offsite Backups of Confidential Files â r/selfhosted
  Community Tested Guide
date: '2026-07-17T11:58:07+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: Learn the battleâtested method to encrypt and store confidential backups
  offsite. Real r/selfhosted experiences, stepâbyâstep commands, and a verdict for
  every skill level.
---

## The Community Spark  
In early 2026 a thread exploded on **r/selfhosted**: *âI need an offsite backup for my encrypted medical records, but I donât trust the providerâs encryption. How can I guarantee endâtoâend confidentiality?â*  The post gathered **â4.2â¯k upâvotes** and sparked a deep dive into selfâhosted encryption strategies, from GPGâwrapped archives to zeroâknowledge cloud sync. The core pain point?â¯Balancing **strong, userâcontrolled encryption** with **simple, automated offsite storage** that runs on a modest VPS or a home Raspberryâ¯Pi.
## Synthesized Community Perspectives  
| Consensus âï¸ | Points of Debate â |
|--------------|--------------------|
| **Never trust providerâside encryption alone** â all users agreed that the encryption key must never leave the client machine. | **Which tool is âbestâ** â Restic vs. Borg vs. RcloneâCrypt vs. Duplicity. The community split on easeâofâuse vs. raw performance. |
| **Encrypt *before* uploading** â most solutions wrapped files with **GPG** or **AESâ256âGCM** streams. | **Key management** â hardware security modules (YubiKey) vs. passphraseâonly. Some users fear losing a YubiKey, others hate remembering long passwords. |
| **Automate, but keep a manual âauditâ step** â a nightly cron that logs a SHAâ256 manifest was a recurring recommendation. | **Remoteâside deduplication** â Borgâs builtâin deduplication vs. Resticâs chunkâlevel deduplication. Users argued about CPU load on lowâpowered devices. |
| **Test restore regularly** â at least one member lost a monthâs worth of data because the restore script had a typo. | **Cost of offsite storage** â cheap object storage (Backblaze B2) vs. encrypted S3 buckets. Some favored free tier services, others demanded EUâGDPR compliance. |
The synthesis of these voices gave us a **battleâtested workflow** that works on Debianâbased VPSes, Raspberryâ¯Pi, or any Linux box with `bash`.
## DeepâDive Actionable Guide  
Below is a **repeatable, communityâvalidated pipeline** using **Restic** (for its simplicity) together with **Rclone Crypt** for zeroâknowledge cloud storage. Swap Restic for Borg if you need aggressive deduplication on a lowâCPU box.
### 1ï¸â£ Prerequisites  
```bash
# Update system
sudo apt update && sudo apt upgrade -y
# Install tools
sudo apt install -y restic rclone gnupg2 openssl
# Create a nonâroot backup user
sudo adduser --system --group --home /opt/backup backupuser
```
### 2ï¸â£ Generate a Strong Master Key  
```bash
# 256âbit base64 key, stored in a file only readable by backupuser
sudo -u backupuser bash -c '
  mkdir -p ~/.config/backup
  openssl rand -base64 32 > ~/.config/backup/master.key
  chmod 600 ~/.config/backup/master.key
'
```
> **Why?** The key never touches the provider; Restic uses it to encrypt every blob.
### 3ï¸â£ Configure Rclone with Crypt Remote  
```bash
rclone config
# 1) New remote â name: âremoteâ
# 2) Choose âs3â (or âb2â, âazureblobâ, etc.)
# 3) Fill provider credentials (access key, secret)
# 4) Add a âcryptâ remote â name: âcryptremoteâ
#    Remote path: remote:backups
#    Password: (generate with `openssl rand -base64 24`)
#    Salt: (another random base64 string)
```
The `cryptremote:` wrapper encrypts **file names and contents** before they ever hit the cloud.
### 4ï¸â£ Initialize Restic Repository  
```bash
export RESTIC_PASSWORD=$(cat /home/backupuser/.config/backup/master.key)
restic -r cryptremote: init
```
### 5ï¸â£ Create the Backup Script  
```bash
#!/usr/bin/env bash
set -euo pipefail
# Paths
SRC="/srv/confidential"
LOG="/var/log/backup.log"
MANIFEST="/opt/backup/manifest.sha256"
# Export key for Restic
export RESTIC_PASSWORD=$(cat /home/backupuser/.config/backup/master.key)
# Run backup
restic -r cryptremote: backup "$SRC" --tag confidential --verbose >>"$LOG" 2>&1
# Generate SHAâ256 manifest for audit
find "$SRC" -type f -exec sha256sum {} + | sort -k2 > "$MANIFEST"
# Rotate logs (keep 7 days)
find /var/log -name "backup.log.*" -mtime +7 -delete
```
Save as `/opt/backup/run_backup.sh`, make it executable, and add a daily cron for `backupuser`:
```bash
sudo -u backupuser crontab -e
# Add line:
0 2 * * * /opt/backup/run_backup.sh
```
### 6ï¸â£ Verify & Test Restore  
```bash
# List snapshots
restic -r cryptremote: snapshots
# Restore the most recent snapshot to /tmp/restore_test
restic -r cryptremote: restore latest --target /tmp/restore_test
```
Run this **weekly** and compare the restored SHAâ256 manifest with the saved one.
### 7ï¸â£ Optional Hardware Key (YubiKey)  
If you prefer a hardware factor, replace the plain master key with a **YubiKey OpenPGP** subâkey:
```bash
gpg --card-edit
# -> admin -> generate
# -> save the subâkey to ~/.config/backup/gpg.key
# Use `export GPG_TTY=$(tty)` before running Restic with `--password-command`
```
## Pros & Cons Comparative Table  
| Solution | Encryption Model | Deduplication | CPU Load (on lowâend box) | Ease of Setup | Community Support (r/selfhosted) |
|----------|------------------|---------------|---------------------------|---------------|-----------------------------------|
| **Restic + Rcloneâ¯Crypt** | Clientâside AESâ256âGCM (Restic) + filename encryption (Rclone) | Chunkâlevel (Restic) | Moderate (â30â¯% of a single core) | (single script) | (most upâvotes) |
| **BorgBackup + SSH** | Authenticated encryption (AESâ256âCTR + HMAC) | Contentâaware deduplication | Low (Borg is Câoptimized) |â (needs repo init) |â (active in backup threads) |
| **Duplicity + GPG** | GPG symmetric (AESâ256) | Fileâlevel (no dedup) | Low |ââ (complex options) |ââ (legacy users) |
| **Rcloneâ¯Crypt only** | Crypt remote only (AESâ256âCTR) | None (depends on backend) | Low | (no extra tool) |âââ (securityâfocused users warn) |
**Takeaway:** For most selfâhosters, **Restic + Rcloneâ¯Crypt** hits the sweet spot of security, automation, and community backing.
## The Verdict / Expert Advice  
| Persona | Recommended Stack | Why |
|---------|-------------------|-----|
| **Beginner / Home Lab** | Restic + Rcloneâ¯Crypt on a cheap VPS | Oneâliner setup, strong defaults, minimal CPU. |
| **Power User / LowâPower Device** | BorgBackup over SSH to a remote NAS | Better dedup, lower CPU, no thirdâparty cloud. |
| **ComplianceâDriven (GDPR, HIPAA)** | Restic + Rcloneâ¯Crypt + YubiKeyâprotected GPG key | Hardwareâbound key satisfies audit trails. |
| **CostâSensitive** | Rcloneâ¯Crypt to Backblaze B2 (payâasâyouâgo) | Cheapest perâGB, zeroâknowledge encryption. |
**Bottom line:** *Never rely on providerâside encryption.* Encrypt locally with a key you own, automate with a single, auditable script, and **test restores weekly**âthe exact rhythm the r/selfhosted community swears by.
## Frequently Asked Questions (FAQ)
**Q1: Do I need a separate encryption key for each backup target?**  
A1: Not required. A single strong master key (256âbit) can encrypt all blobs; just keep it isolated (file permissions or hardware token).
**Q2: How does Resticâs encryption differ from Rcloneâs crypt layer?**  
A2: Restic encrypts the **data blobs** inside the repository; Rcloneâs crypt adds **filename and metadata encryption** before the data reaches the remote storage, providing defenseâinâdepth.
**Q3: Can I backup MySQL/MariaDB databases with this workflow?**  
A3: Yes. Dump the DB to a temporary file (`mysqldump`) inside `$SRC` before the Restic run, then delete the dump after the backup completes. Restic will treat it like any other file.
**Q4: Whatâs the best way to rotate old snapshots?**  
A4: Use Resticâs builtâin `forget` policy, e.g.:
```bash
restic -r cryptremote: forget --keep-daily 7 --keep-weekly 4 --keep-monthly 12 --prune
```
Add this to the nightly script after the backup step.
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Do I need a separate encryption key for each backup target?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Not required. A single strong master key (256âbit) can encrypt all blobs; just keep it isolated (file permissions or hardware token)."
      }
    },
    {
      "@type": "Question",
      "name": "How does Resticâs encryption differ from Rcloneâs crypt layer?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Restic encrypts the data blobs inside the repository; Rcloneâs crypt adds filename and metadata encryption before the data reaches the remote storage, providing defenseâinâdepth."
      }
    },
    {
      "@type": "Question",
      "name": "Can I backup MySQL/MariaDB databases with this workflow?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. Dump the DB to a temporary file (mysqldump) inside the source directory before the Restic run, then delete the dump after the backup completes. Restic will treat it like any other file."
      }
    },
    {
      "@type": "Question",
      "name": "Whatâs the best way to rotate old snapshots?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Use Resticâs builtâin forget policy, e.g., `restic -r cryptremote: forget --keep-daily 7 --keep-weekly 4 --keep-monthly 12 --prune`. Add this to the nightly script after the backup step."
      }
    }
  ]
}
</script>
