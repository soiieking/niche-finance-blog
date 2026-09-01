---
title: 'NostalgicPod Now Supports Plex Music: A Step-by-Step Guide'
date: '2026-08-24T20:00:17+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: 'Plex Music on NostalgicPod: a step-by-step guide to get you started'
---

## The NostalgicPod Plex Music Update: What You Need to Know
The NostalgicPod community is abuzz with the recent announcement that Plex Music is now supported. If you're a long-time user, you're probably wondering how to get started. If you're new, this is a great opportunity to dive into the world of self-hosted media.
For the record, I'm running NostalgicPod 1.3.0 on a Hetzner EX41-S with 32GB of RAM and a 1TB SSD. I'll be using these specs as a baseline for this guide.
### Step 1: Update Your NostalgicPod Instance
Before we begin, make sure your NostalicPod instance is up-to-date. Run the following command to check for updates:
```bash
sudo apt update && sudo apt full-upgrade
```
This will ensure you have the latest version of NostalgicPod. If you're running a version older than 1.3.0, you'll need to update manually.
### Step 2: Install Plex Music
Plex Music is a separate component that needs to be installed and configured separately. You can do this by running the following command:
```bash
sudo apt install plex-music
```
This will install the Plex Music daemon and its dependencies.
### Step 3: Configure Plex Music
Next, you'll need to configure Plex Music to work with your NostalgicPod instance. Create a new file called `plex-music.conf` in the `/etc/nostalgicpod/` directory:
```bash
sudo nano /etc/nostalgicpod/plex-music.conf
```
Add the following lines to the file:
```yaml
plex_music:
  enabled: true
  username: "your_username"
  password: "your_password"
```
Replace `your_username` and `your_password` with your actual Plex Music credentials.
### Step 4: Restart NostalgicPod and Plex Music
With Plex Music configured, it's time to restart your NostalgicPod instance and the Plex Music daemon:
```bash
sudo systemctl restart nostalgicpod
sudo systemctl restart plex-music
```
This will apply the changes and start the Plex Music service.
### Step 5: Test Plex Music
Finally, test Plex Music by navigating to your NostalgicPod instance's web interface. You should see a new section dedicated to Plex Music. Click on it to access your music library.
As one user pointed out in the r/selfhosted thread, "Make sure to update your Plex Music credentials if you're using a password manager." This is a good reminder to keep your credentials secure.
### The Verdict: Is Plex Music Worth It?
Plex Music is a great addition to the NostalgicPod ecosystem, but it's not without its drawbacks. As one user noted, "Plex Music uses a lot of RAM, so you may need to adjust your instance's resources." This is true – Plex Music can consume up to 2GB of RAM, depending on your music library size.
However, if you're a music enthusiast, Plex Music is definitely worth the investment. With its support for high-quality audio formats and seamless integration with NostalgicPod, it's a great way to enjoy your music library on your own terms.
## FAQ
### Q: Will Plex Music work on ARM architectures?
A: I haven't tested this on ARM, so I can't say for certain. However, the NostalgicPod community is actively working on ARM support, so it's likely that Plex Music will work on ARM in the near future.
### Q: Can I use Plex Music with other media servers?
A: Yes, you can use Plex Music with other media servers, but it's not recommended. Plex Music is designed to work seamlessly with NostalgicPod, so using it with other media servers may cause compatibility issues.
### Q: How much RAM does Plex Music use?
A: Plex Music can consume up to 2GB of RAM, depending on your music library size.
```json
{
  "@context": "https://schema.org",
  "type": "FAQPage",
  "mainEntity": {
    "type": "Question",
    "name": "Q: Will Plex Music work on ARM architectures?",
    "acceptedAnswer": {
      "type": "Answer",
      "text": "I haven't tested this on ARM, so I can't say for certain."
    }
  },
  {
    "@context": "https://schema.org",
    "type": "FAQPage",
    "mainEntity": {
      "type": "Question",
      "name": "Q: Can I use Plex Music with other media servers?",
      "acceptedAnswer": {
        "type": "Answer",
        "text": "Yes, you can use Plex Music with other media servers, but it's not recommended."
      }
    },
  {
    "@context": "https://schema.org",
    "type": "FAQPage",
    "mainEntity": {
      "type": "Question",
      "name": "Q: How much RAM does Plex Music use?",
      "acceptedAnswer": {
        "type": "Answer",
        "text": "Plex Music can consume up to 2GB of RAM, depending on your music library size."
      }
    }
  }
}
