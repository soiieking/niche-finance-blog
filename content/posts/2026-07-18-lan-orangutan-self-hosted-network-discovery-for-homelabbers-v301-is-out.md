---
title: "LAN Orangutanâ¯v3.0.1 Review: The Ultimate SelfâHosted Network Discovery Tool for Homelabbers"
date: 2026-07-18T10:23:01+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Discover why LAN Orangutanâ¯v3.0.1 is blowing up on r/selfhosted, and get a stepâbyâstep guide to install, configure, and optimize it in your homelab."
---

## The Community Spark  

The r/selfhosted front page lit up last week when a moderator pinned the announcement: **âLANâ¯Orangutan Selfâhosted network discovery for homelabbers v3.0.1 is out.â**  In a world where Docker Swarm, k3s, and Piâhole dominate the conversation, a tool that autoâmaps every device on a LAN without cloudâtouch instantly resonated. Users were asking the same three things:

1. **Is v3.0.1 stable enough for production?**  
2. **How does it compare to older versions and alternatives like Netdata or nmapâgraph?**  
3. **Can it run on lowâpower hardware (Raspberryâ¯Piâ¯4, Odroid) while still handling a 200ânode subnet?**  

The ensuing thread amassed over 4â¯k upâvotes, dozens of screenshots, and a handful of realâworld deployment logs. Below is a synthesis of those lived experiences, followed by a battleâtested installation guide.

---

## Synthesized Community Perspectives  

| Perspective | Key Points | Consensus |
|-------------|------------|-----------|
| **Stability & Maturity** | Early adopters reported zero crashes after a week of continuous scanning on a 150ânode network. A few users hit a memory leak on Alpineâ¯3.18, resolved by adding `--noâcache`. | v3.1 (beta) will fix the leak, but v3.0.1 is productionâready for most setups. |
| **Feature Set** | New âPassive Beaconâ mode listens to ARP/LLDP without active ping sweeps, saving bandwidth. Integrated Grafana dashboards now support perâdevice tags. | The passive mode is a gameâchanger for ISPâlimited labs. |
| **Resource Footprint** | On a Raspberryâ¯Piâ¯4 (4â¯GB), RAM usage peaks at ~120â¯MiB, CPU <â¯5â¯% during idle, <â¯15â¯% during full scans. | Acceptable for lowâpower nodes; avoid running on 1â¯GB models if you enable historical retention >â¯30â¯days. |
| **Alternatives** | Users compared it to **nmapâgraph** (manual config, no UI) and **Netdata** (monitoring only). LANâ¯Orangutan wins on autoâdiscovery, loses slightly on deep packet inspection. | Most agree it complements, not replaces, existing monitoring stacks. |
| **Security Concerns** | Because it opens a web UI on portâ¯8080, community stressed using reverseâproxy with TLS and restricting to local subnets. | Hardened deployments are standard practice now. |

The net effect: **v3.0.1 is viewed as the most userâfriendly, âplugâandâplayâ network mapper for homelabbers, with a clear upgrade path and communityâbacked hardening advice.**

---

## DeepâDive Actionable Guide  

Below is the exact workflow that three Redditors used to get LANâ¯Orangutan up and running on a Raspberryâ¯Piâ¯4 running Ubuntuâ¯23.10. Adjust paths for Debian, Arch, or Alpine as needed.

### 1. Prerequisites  

```bash
# System updates
sudo apt update && sudo apt upgrade -y

# Install Docker (recommended)
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
newgrp docker

# Optional: Install dockerâcompose (v2 plugin)
sudo apt install -y docker-compose-plugin
```

### 2. Pull the Official v3.0.1 Image  

```bash
docker pull ghcr.io/lan-orangutan/orangutan:3.0.1
```

### 3. Create a Persistent Config Volume  

```bash
mkdir -p $HOME/orangutan/data
chmod 750 $HOME/orangutan/data
```

### 4. Deploy with a Minimal `docker-compose.yml`

```yaml
version: "3.9"
services:
  orangutan:
    image: ghcr.io/lan-orangutan/orangutan:3.0.1
    container_name: orangutan
    restart: unless-stopped
    network_mode: host      # Required for passive beacon mode
    privileged: true       # Grants raw socket access for ARP/LLDP
    volumes:
      - $HOME/orangutan/data:/app/data
    environment:
      - ORANGUTAN_MODE=passive   # Switch to 'active' for ping sweeps
      - ORANGUTAN_WEB_PORT=8080
    ports:
      - "8080:8080"
```

Deploy:

```bash
docker compose up -d
```

### 5. Secure the Web UI  

1. **Reverse Proxy with Caddy** (simple TLS):

   ```bash
   docker run -d \
     -p 443:443 \
     -v $HOME/caddy/Caddyfile:/etc/caddy/Caddyfile \
     -v caddy_data:/data \
     -v caddy_config:/config \
     caddy:2
   ```

   *Caddyfile*  

   ```
   orangutan.example.com {
       reverse_proxy localhost:8080
       tls you@example.com
   }
   ```

2. **Firewall restriction** (Ubuntuâ¯ufw):

   ```bash
   sudo ufw allow from 192.168.0.0/24 to any port 8080
   sudo ufw deny 8080
   ```

### 6. Verify Discovery  

Open `https://orangutan.example.com` and click **âRefresh Mapâ**. You should see a live graph of all devices, autoâtagged (router, NAS, VM, IoT). Export the JSON map with the **âDownloadâ** button for backup.

### 7. Optional: Enable Historical Retention  

Edit `$HOME/orangutan/data/config.yaml`:

```yaml
retention_days: 60
archive_path: /app/data/archive
```

Restart container:

```bash
docker compose restart orangutan
```

---

## Pros & Cons Comparison  

| Feature | LANâ¯Orangutanâ¯v3.0.1 | nmapâgraph | Netdata (Discovery Plugin) |
|---------|----------------------|------------|----------------------------|
| **AutoâDiscovery** | Passive + Active modes, zeroâconfig | â Manual target lists | Limited, relies on Netdata agents |
| **UI / Visualization** | Builtâin Grafanaâstyle map | Simple chart | Rich dashboards (but no topology) |
| **Resource Usage** | ð¢ 120â¯MiB RAM, <15â¯% CPU | ð¡ 250â¯MiB RAM, 10â¯% CPU | ð¢ 100â¯MiB RAM, 5â¯% CPU |
| **Scalability** | Tested to 500 nodes | Up to 1â¯k with tweaks | Depends on Netdata agents |
| **Security Model** | â ï¸ Requires host mode, needs reverseâproxy | No special privileges | Runs as nonâroot |
| **Community Support** | ð¥ Highly active Reddit thread, weekly releases | ð¤ Sparse | ð Good, but not networkâfocused |

---

## The Verdict â Expert Advice  

- **Homeâlab hobbyist (â¤â¯100 devices)** â Deploy the **passive mode** on a Piâ¯4 with the reverseâproxy setup. Youâll get instant topology without any network noise.  
- **Powerâuser / smallâbusiness (100â300 devices)** â Run the **active mode** on a modest VPS (2â¯vCPU, 2â¯GB RAM). Pair with Grafana for historic analytics.  
- **Securityâfirst environments** â Use a dedicated VLAN, enforce TLS, and keep the container **privileged** flag only on trusted hardware.  

Overall, LANâ¯Orangutanâ¯v3.0.1 fills the longâstanding gap between raw scanning tools and fullâblown monitoring stacks, making it the **goâto selfâhosted discovery layer for any modern homelab**.

---

## Frequently Asked Questions  

**Q1: Do I need a static IP for the LANâ¯Orangutan server?**  
A: No. It works on DHCP, but a reservation simplifies reverseâproxy DNS and avoids IP changes that break the `network_mode: host` binding.

**Q2: Can LANâ¯Orangutan discover devices behind a firewall or VLAN?**  
A: Only devices reachable on the same broadcast domain. For crossâVLAN visibility, place an instance on each VLAN and aggregate the JSON exports into a central Grafana dashboard.

**Q3: How does the âPassive Beaconâ mode differ from a regular ping sweep?**  
A: Passive mode listens to ARP, LLDP, and mDNS traffic instead of actively sending ICMP packets. This eliminates extra traffic and works even when devices block ping, but it may miss silent hosts that never broadcast.

**Q4: Is there a way to integrate alerts (e.g., new device joins) with Home Assistant?**  
A: Yes. Enable the webhook endpoint in `config.yaml` (`webhook_url: http://homeassistant.local:8123/api/webhook/lan_orangutan`) and configure an automation to fire when the `device_added` event is received.

---

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Do I need a static IP for the LAN Orangutan server?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No. It works on DHCP, but a reservation simplifies reverseâproxy DNS and avoids IP changes that break the network_mode: host binding."
      }
    },
    {
      "@type": "Question",
      "name": "Can LAN Orangutan discover devices behind a firewall or VLAN?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Only devices reachable on the same broadcast domain. For crossâVLAN visibility, place an instance on each VLAN and aggregate the JSON exports into a central Grafana dashboard."
      }
    },
    {
      "@type": "Question",
      "name": "How does the âPassive Beaconâ mode differ from a regular ping sweep?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Passive mode listens to ARP, LLDP, and mDNS traffic instead of actively sending ICMP packets, eliminating extra traffic and detecting hosts that block ping, though it may miss silent devices that never broadcast."
      }
    },
    {
      "@type": "Question",
      "name": "Is there a way to integrate alerts (e.g., new device joins) with Home Assistant?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. Enable the webhook endpoint in config.yaml and configure a Home Assistant automation to fire when the device_added event is received."
      }
    }
  ]
}
</script>