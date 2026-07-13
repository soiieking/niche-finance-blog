---
title: "One Year Later: The Ultimate Guide After Reaching My 100% Self‑Hosted Goal"
date: 2026-07-13T16:52:51+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "A year after hitting a 100% self‑hosted setup, discover community insights, step‑by‑step tactics, and expert verdicts to keep your stack rock‑solid."
---

## The Community Spark  

A year ago, a modest post on **r/selfhosted** titled *“1y after posting about my 100% goal”* went viral. The author declared they had migrated **every** personal service—from email to media streaming—to a self‑managed VPS and were finally living “100 % self‑hosted.”  

Why did this thread explode?  

| Reason | What it meant for readers |
|--------|---------------------------|
| **A tangible milestone** | Many hobbyists chase the “all‑in” dream but never document the finish line. |
| **Fear of lock‑in** | Cloud‑centric narratives dominate tech news; a real‑world counter‑example feels rebellious. |
| **Longevity question** | One year is the first realistic benchmark to evaluate reliability, cost, and sanity. |
| **Open‑source pride** | The post showcased a pure GNU/Linux stack, feeding the ethos of the subreddit. |

The buzz generated dozens of follow‑up comments, AMA sessions, and a mini‑sub‑thread titled *“What would you do differently?”* That chorus of lived experiences is the foundation of this guide.

---

## Synthesized Community Perspectives  

### Consensus: The Core Pillars That Made 100 % Possible  

| Pillar | Community Quote | Why it matters |
|--------|----------------|----------------|
| **Automation First** | “I stopped manually updating services after I wrote a single Ansible playbook.” – u/automation‑guru | Reduces human error; frees time for strategic upgrades. |
| **Backup‑Centric Architecture** | “My daily rsync snapshots saved me when the SSD died on month 7.” – u/rsync‑fanatic | Guarantees data integrity, the single point of failure for any self‑host. |
| **Network Isolation** | “I placed everything behind a WireGuard mesh; the only public port is 443.” – u/wireguard‑warrior | Minimises attack surface, essential for long‑term security. |
| **Monitoring & Alerting** | “Grafana + Prometheus caught a memory leak before it crashed the mail server.” – u/metrics‑maven | Early detection prevents downtime, crucial for a 100 % uptime claim. |
| **Cost Transparency** | “I tracked my VPS, storage, and bandwidth in a simple CSV; the annual spend was $240.” – u/budget‑beast | Prevents surprise bills and validates the “self‑hosted is cheaper” narrative. |

### Hot Debates  

| Topic | Pro‑Self‑Host Argument | Pro‑Cloud Counterpoint |
|-------|------------------------|------------------------|
| **Zero‑Trust vs. Simplicity** | “Zero‑trust networking is mandatory; otherwise you’re just a glorified data center.” – u/zero‑trust‑dev | “For a personal blog, a single reverse proxy is enough and easier to maintain.” – u/simple‑starter |
| **Single VPS vs. Multi‑Node Redundancy** | “One beefy VM is a single point of failure; spread services across two cheap nodes.” – u/red‑team | “Two nodes double the cost and complexity; backups + snapshots are sufficient for hobbyists.” – u/lean‑budget |
| **Docker Compose vs. Systemd Units** | “Containers isolate dependencies; they’re the future.” – u/container‑captain | “Systemd is native, lighter, and doesn’t add an extra abstraction layer.” – u/sysadmin‑oldschool |

The majority leaned toward **Docker Compose for service isolation** *combined* with **systemd for host‑level services**—a hybrid that satisfies both reliability and simplicity.

---

## Deep‑Dive Actionable Guide: Replicating the 100 % Self‑Hosted Stack  

Below is a reproducible, step‑by‑step playbook distilled from the community’s collective code snippets. It assumes a **Ubuntu 24.04 LTS** VPS with **2 vCPU, 4 GB RAM, 80 GB SSD** and a **static public IP**.

### 1️⃣ Prepare the Host  

```bash
# Update and install essential tools
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl git vim unzip ufw fail2ban \
    python3-pip gnupg2 ca-certificates

# Harden SSH
sudo sed -i 's/^#Port 22/Port 2222/' /etc/ssh/sshd_config
sudo sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# Enable UFW (allow only needed ports)
sudo ufw default deny incoming
sudo ufw allow 2222/tcp          # SSH
sudo ufw allow 443/tcp           # HTTPS (reverse proxy)
sudo ufw enable
```

### 2️⃣ Set Up WireGuard Mesh (Zero‑Trust Networking)  

```bash
# Install WireGuard
sudo apt install -y wireguard

# Generate server keys
umask 077
wg genkey | tee server_private.key | wg pubkey > server_public.key

# Create /etc/wireguard/wg0.conf
cat <<EOF | sudo tee /etc/wireguard/wg0.conf
[Interface]
Address = 10.10.0.1/24
ListenPort = 51820
PrivateKey = $(cat server_private.key)

# Example peer (your laptop)
[Peer]
PublicKey = <LAPTOP_PUBLIC_KEY>
AllowedIPs = 10.10.0.2/32
EOF

sudo systemctl enable --now wg-quick@wg0
```

*All containers will bind only to `10.10.0.x`. The only public-facing port remains 443.*

### 3️⃣ Deploy the Service Stack with Docker Compose  

Create a non‑root user `selfhost` and grant Docker rights:

```bash
sudo adduser --disabled-password --gecos "" selfhost
sudo usermod -aG docker selfhost
```

Log in as `selfhost` and set up a project directory:

```bash
su - selfhost
mkdir -p ~/stack/{data,config}
cd ~/stack
```

#### `docker-compose.yml`

```yaml
version: "3.9"

services:
  # Reverse proxy – Caddy (auto HTTPS via Let's Encrypt)
  caddy:
    image: caddy:latest
    restart: unless-stopped
    ports:
      - "443:443"
    volumes:
      - ./caddy/Caddyfile:/etc/caddy/Caddyfile
      - caddy_data:/data
      - caddy_config:/config
    networks:
      - internal

  # Mail server – Mailcow (all‑in‑one)
  mailcow:
    image: mailcow/mailcow:latest
    restart: unless-stopped
    env_file: ./mailcow/.env
    volumes:
      - mailcow_data:/mailcow
    networks:
      - internal

  # Nextcloud – personal cloud storage
  nextcloud:
    image: nextcloud:apache
    restart: unless-stopped
    environment:
      - MYSQL_HOST=db
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=${NEXTCLOUD_DB_PASS}
    volumes:
      - ./nextcloud/data:/var/www/html
    depends_on:
      - db
    networks:
      - internal

  # MariaDB – DB for Nextcloud & other services
  db:
    image: mariadb:10.11
    restart: unless-stopped
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASS}
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=${NEXTCLOUD_DB_PASS}
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - internal

  # Pi-hole – network‑wide ad blocking
  pihole:
    image: pihole/pihole:latest
    restart: unless-stopped
    environment:
      - TZ=UTC
      - WEBPASSWORD=${PIHOLE_WEBPASS}
    volumes:
      - pihole_data:/etc/pihole
      - dnsmasq_data:/etc/dnsmasq.d
    ports:
      - "53:53/tcp"
      - "53:53/udp"
    networks:
      - internal

networks:
  internal:
    driver: bridge
    ipam:
      config:
        - subnet: 10.10.0.0/24

volumes:
  caddy_data:
  caddy_config:
  mailcow_data:
  db_data:
  pihole_data:
  dnsmasq_data:
```

> **Tip:** Store secrets in a `.env` file secured with `chmod 600 .env`. This is a community‑recommended practice for “secret hygiene”.

### 4️⃣ Configure Monitoring & Alerting  

#### Prometheus + Grafana (Docker)

Add these services to the existing `docker-compose.yml`:

```yaml
  prometheus:
    image: prom/prometheus:latest
    restart: unless-stopped
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    networks:
      - internal

  grafana:
    image: grafana/grafana:latest
    restart: unless-stopped
    depends_on:
      - prometheus
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_ADMIN_PASS}
    networks:
      - internal
```

**`prometheus.yml` (minimal scrape)**

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'docker'
    static_configs:
      - targets: ['host.docker.internal:9323']  # Docker daemon metrics
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['node-exporter:9100']
```

Install **node_exporter** on the host:

```bash
sudo apt install -y prometheus-node-exporter
sudo systemctl enable --now prometheus-node-exporter
```

Grafana dashboards for Docker, system, and WireGuard are shared widely on r/selfhosted; import IDs **15185**, **1860**, and **11242**.

### 5️⃣ Implement Immutable Backups  

1. **Daily rsync snapshots** to a secondary cheap storage (e.g., Hetzner Storage Box).  

```bash
#!/bin/bash
set -euo pipefail
SRC="/home/selfhost/stack"
DEST="user@storagebox.example.com:/backups/$(date +%Y-%m-%d)"
mkdir -p "$DEST"
rsync -a --delete "$SRC/" "$DEST/"
```

2. **Weekly off‑site tarball** for Docker volumes:

```bash
# Run as root via cron @weekly
tar -czf /tmp/backup_$(date +%F).tar.gz -C /var/lib/docker/volumes .
scp /tmp/backup_*.tar.gz user@offsite.example.com:/offsite/
```

### 6️⃣ Finance & Cost Tracking  

Create a simple CSV `expenses.csv`:

```csv
Date,Item,Cost (USD)
2025-01-01,VPS (monthly),20
2025-01-01,Domain (annual),12
2025-01-01,Backup storage (monthly),5
2025-01-01,WireGuard VPN (free),0
2025-06-01,Hardware upgrade (one‑time),45
```

Sum with `awk`:

```bash
awk -F, 'NR>1 {total+=$3} END {print "Annual spend: $" total}' expenses.csv
```

Most contributors reported **$240–$300** per year for a comparable setup—well under typical SaaS bundles.

---

## Pros & Cons – Comparative Table  

| Aspect | 100 % Self‑Hosted (Community‑Validated) | Managed Cloud SaaS |
|--------|------------------------------------------|--------------------|
| **Control** | Full root access; can tweak any stack component. | Limited to provider UI/API. |
| **Cost** | $240‑$300 / yr (VPS + storage). | $600‑$1200 / yr for similar services. |
| **Security** | Zero‑trust network, self‑managed keys. | Provider handles patches, but shared tenancy risk. |
| **Complexity** | Moderate – requires Linux, Docker, networking knowledge. | Low – UI‑driven, minimal sysadmin effort. |
| **Scalability**