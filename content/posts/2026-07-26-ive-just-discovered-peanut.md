---
title: 'Discovering PeaNUT: The Ultimate NUT UPS Monitoring Tool for Self-Hosted Home
  Labs'
date: '2026-07-26T23:36:55+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Discovering PeaNUT: The Ultimate NUT UPS Monitoring Tool for
  Self-Hosted Home Labs.'
---

## The Community Spark: Why r/selfhosted is Raving About PeaNUT
If you’ve spent any time in the `r/selfhosted` subreddit recently, you’ve likely seen the sudden surge of users exclaiming, "I’ve just discovered PeaNUT!" For years, the gold standard for managing Uninterruptible Power Supplies (UPS) has been Network UPS Tools (NUT). However, NUT is infamous for its archaic configuration syntax, fragmented documentation, and lack of a modern, unified dashboard. 
The core problem the community faces is simple: How do we reliably monitor battery backups across multiple nodes, trigger safe shutdowns during outages, and actually understand our power metrics without fighting with terminal configurations for hours? Enter PeaNUT—a modern, Docker-friendly wrapper and dashboard for NUT.
## Synthesized Community Perspectives
The consensus on Reddit is clear: NUT is incredibly powerful but practically indecipherable for the average self-hoster. Users shared collective war stories of spending hours debugging `ups.conf` and `upsd.conf` permissions, only to find their USB connections dropping upon reboot.
### The USB Passthrough Struggle
A major debate in the community revolves around Docker USB passthrough. Many users initially struggled to pass their UPS (commonly CyberPower or APC models) into the container. The community consensus? You must pass the specific host USB device using `--device=/dev/bus/usb/XXX/YYY` or the broader `/dev/bus/usb` path, and crucially, run the container with elevated privileges or specific PUID/PGID settings to access hardware.
### Dashboard Envy
Another shared experience is the relief of having a graphical UI. While `upscmd` works, real-time visual graphs of input voltage, battery charge, and load percentages provide immense peace of mind. Users agreed that PeaNUT bridges the gap between enterprise-grade power management and home-lab accessibility.
## Deep-Dive Actionable Guide: Setting Up PeaNUT in Docker
Setting up PeaNUT requires a working NUT configuration, but the container itself simplifies the delivery. Here is a practical, community-tested `docker-compose.yml` to get your UPS monitoring up and running.
### Step 1: Create Your Docker Compose File
Create a directory for PeaNUT and create a `docker-compose.yml` file:
```yaml
version: '3.8'
services:
  peanut:
    image: brandawg/peanut:latest
    container_name: peanut
    restart: unless-stopped
    # Pass the USB device to the container. Find your device ID with 'lsusb'
    devices:
      - /dev/bus/usb:/dev/bus/usb
    # PeaNUT requires privileged mode to read hardware IDs from the UPS
    privileged: true
    volumes:
      - ./nut-config:/etc/nut
      - ./data:/data
    environment:
      - TZ=America/New_York
      - PORT=8080
    ports:
      - "8080:8080"
```
### Step 2: Configure NUT Inside the Container
While PeaNUT provides the UI, it still relies on underlying NUT configurations. Edit the `ups.conf` file inside your mounted `./nut-config` directory:
```ini
# /etc/nut/ups.conf
[user]
    driver = usbhid-ups
    port = auto
    desc = "Home Lab Rack UPS"
    # Poll every 15 seconds to reduce USB overhead
    pollinterval = 15
```
### Step 3: Start the Container
Fire up the container and check the logs:
```bash
docker-compose up -d
docker logs -f peanut
```
If configured correctly, you should see the NUT driver successfully initialize your UPS. Navigate to `http://your-server-ip:8080` to view the dashboard.
## Pros & Cons / Comparative Table
How does PeaNUT stack up against a bare-metal NUT installation or proprietary tools like APC PowerChute?
| Feature | PeaNUT | Bare-Metal NUT | APC PowerChute / Vendor SW |
| :--- | :--- | :--- | :--- |
| **UI / Dashboard** | Modern, React-based UI | CLI Only (`upsc`) | Basic, often bloated |
| **Docker Integration** | Native & Streamlined | Requires manual host setup | Poor / Often unsupported |
| **Cross-Brand Support** | Yes (APC, CyberPower, etc.) | Yes | No (Vendor locked) |
| **Setup Difficulty** | Moderate | High | Low to Moderate |
| **Custom Alerting** | Webhooks / Email via UI | Custom shell scripts | Proprietary email only |
## The Verdict / Expert Advice
If you are running a modern, containerized home lab, **Peanut is the definitive solution for UPS monitoring**. 
*   **For Beginners:** Stick to PeaNUT's default Docker settings. Do not attempt to run NUT bare-metal on Proxmox or Unraid if you are new to Linux; Docker containerization prevents NUT's quirks from breaking your host system.
*   **For Advanced Users:** Leverage PeaNUT alongside Home Assistant. Point Home Assistant's NUT integration to your PeaNUT container's data port to trigger smart home automations (like turning off non-essential servers) when the battery drops below 50%.
## Frequently Asked Questions (FAQ)
**What is PeaNUT used for?**
Peanut is a self-hosted web dashboard and configuration tool for Network UPS Tools (NUT). It allows home lab operators to monitor their UPS battery status, voltage, and load, and to safely shut down servers during a power outage from a clean web interface.
**Does PeaNUT support APC and CyberPower UPS devices?**
Yes. Because PeaNUT acts as a frontend for NUT, it supports any UPS device compatible with NUT. This includes popular brands like APC, CyberPower, and Tripp Lite, provided the device communicates via USB or SNMP.
**How do I pass a USB UPS to a PeaNUT Docker container?**
You must map the USB device to your container. Use the `lsusb` command on your host to find the Bus and Device ID, then add `devices: - /dev/bus/usb/003/002` (replacing with your actual IDs) to your `docker-compose.yml` file.
**Can PeaNUT trigger automatic server shutdowns?**
Yes. By configuring the `upsd.conf` and `upsmon.conf` files within the PeaNUT configuration directory, you can set thresholds (e.g., battery below 20%) that will trigger a safe shutdown command to your host machine or connected clients.
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is PeaNUT used for?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Peanut is a self-hosted web dashboard and configuration tool for Network UPS Tools (NUT). It allows home lab operators to monitor their UPS battery status, voltage, and load, and to safely shut down servers during a power outage from a clean web interface."
      }
    },
    {
      "@type": "Question",
      "name": "Does PeaNUT support APC and CyberPower UPS devices?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. Because PeaNUT acts as a frontend for NUT, it supports any UPS device compatible with NUT. This includes popular brands like APC, CyberPower, and Tripp Lite, provided the device communicates via USB or SNMP."
      }
    },
    {
      "@type": "Question",
      "name": "How do I pass a USB UPS to a PeaNUT Docker container?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "You must map the USB device to your container. Use the lsusb command on your host to find the Bus and Device ID, then add devices: - /dev/bus/usb/003/002 (replacing with your actual IDs) to your docker-compose.yml file."
      }
    },
    {
      "@type": "Question",
      "name": "Can PeaNUT trigger automatic server shutdowns?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. By configuring the upsd.conf and upsmon.conf files within the PeaNUT configuration directory, you can set thresholds (e.g., battery below 20%) that will trigger a safe shutdown command to your host machine or connected clients."
      }
    }
  ]
}
</script>
