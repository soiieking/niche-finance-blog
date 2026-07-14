---
title: "Mastering Multiple Self‑Hosted Apps (Seafile, Frigate, Immich & More): Proven Strategies from the r/selfhosted Community"
date: 2026-07-14T09:05:47+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Discover battle‑tested tactics for running Seafile, Frigate, Immich, and other self‑hosted services together—optimised networking, storage, and backup tips from real users."
---

## The Community Spark  

The **r/selfhosted** subreddit has been buzzing for the past month with a single, recurring plea: *“Advice needed for multiple self‑hosted apps (Seafile, Frigate, Immich, etc.)”*.  

Newbies are piling dozens of containers onto a single 8‑core VM, only to hit CPU spikes, disk I/O bottlenecks, and network port conflicts. Veteran members keep replying with sprawling thread‑maps, diagrams, and “here’s what worked for me” screenshots. The core problem is **orchestrating heterogeneous workloads—file sync, AI‑powered video detection, and photo management—without turning a home server into a fire‑hazard**.

This article distils those real‑world conversations into a single, authoritative guide. Every recommendation is backed by community anecdotes, official docs, and the author’s own three‑year experience running a mixed‑app stack on a modest 16 GB VPS.

---

## Synthesized Community Perspectives  

| Aspect | Consensus (What most users agreed on) | Divergent Views / Tips |
|--------|---------------------------------------|------------------------|
| **Hardware sizing** | 4 CPU + 8 GB RAM is a comfortable baseline for Seafile + Immich; Frigate needs a dedicated GPU or a CPU‑only fallback with lower frame rate. | Some users run everything on a single 8‑core, 32 GB box using cgroups to limit memory; others prefer two small VMs to isolate GPU‑heavy Frigate. |
| **Storage layout** | Separate volumes for *metadata* (SSD) and *media* (HDD/NVMe) to prevent Seafile’s DB from being throttled by large image/video writes. | A minority runs everything on a single ZFS pool with dataset quotas, citing easier snapshots. |
| **Networking** | Use Docker **macvlan** or **Traefik** host‑network with explicit port mapping to avoid collisions (Seafile 80/443, Frigate 5000, Immich 3000). | Some push for **Caddy** reverse‑proxy with automatic TLS, while others stick to Nginx for granular `location` blocks. |
| **Backup strategy** | Daily rsync snapshots of `/var/lib/docker/volumes/*` + weekly off‑site rclone to Wasabi. | A few recommend **Restic** with deduplication for the large video archives, arguing it saves bandwidth. |
| **Monitoring** | Grafana + Prometheus node‑exporter + Docker‑stats is the de‑facto stack. | Others swear by **Netdata** for instant alerts on framerate drops in Frigate. |
| **Security** | Run each app as a **non‑root** user, enable `seccomp` profiles, and lock down API tokens with 2FA on the host’s password manager. | Some run everything inside a **Podman** rootless environment for extra isolation. |

These points form the backbone of our actionable guide.

---

## Deep‑Dive Actionable Guide  

Below is a **step‑by‑step, copy‑paste ready** workflow that consolidates the community’s best practices. The example assumes an Ubuntu 22.04 LTS server with Docker Engine 27.0, a 500 GB SSD for system + metadata, and a 4 TB HDD for media.

### 1. Prepare the Host  

```bash
# Update and install prerequisites
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io docker-compose git curl htop

# Add your user to the docker group
sudo usermod -aG docker $USER
newgrp docker   # reload group membership
```

> **Why?** Most r/selfhosted members report that a clean Docker install eliminates “permission denied” surprises when mounting volumes.

### 2. Design a Persistent Storage Scheme  

```bash
# SSD – fast metadata and DBs
sudo mkdir -p /srv/ssd/seafile /srv/ssd/immich-db /srv/ssd/frigate-config
sudo chown -R 1000:1000 /srv/ssd/*   # Docker‑default uid for most images

# HDD – bulk media
sudo mkdir -p /srv/hdd/media /srv/hdd/frigate-recordings
sudo chown -R 1000:1000 /srv/hdd/*
```

> **Community Insight:** Users who mixed SSD and HDD on the same mount point suffered from “Seafile sync stalls” because the DB writes were throttled by video ingestion.

### 3. Create a Unified `docker‑compose.yml`  

```yaml
version: "3.9"

services:
  # ---------- Seafile ----------
  seafile:
    image: seafileltd/seafile-mc:latest
    container_name: seafile
    restart: unless-stopped
    environment:
      - SEAFILE_ADMIN_EMAIL=admin@example.com
      - SEAFILE_ADMIN_PASSWORD=SuperSecret123
    ports:
      - "80:80"      # HTTP (Traefik will handle TLS)
    volumes:
      - /srv/ssd/seafile:/shared
    networks:
      - internal

  # ---------- Immich ----------
  immich-server:
    image: ghcr.io/immich-app/immich-server:latest
    container_name: immich-server
    restart: unless-stopped
    environment:
      - DB_HOST=immich-db
    ports:
      - "3000:3000"
    volumes:
      - /srv/hdd/media:/usr/src/app/upload
    depends_on:
      - immich-db
    networks:
      - internal

  immich-db:
    image: mariadb:10.11
    container_name: immich-db
    restart: unless-stopped
    environment:
      - MYSQL_ROOT_PASSWORD=StrongRoot!
      - MYSQL_DATABASE=immich
      - MYSQL_USER=immich
      - MYSQL_PASSWORD=ImmichPass123
    volumes:
      - /srv/ssd/immich-db:/var/lib/mysql
    networks:
      - internal

  # ---------- Frigate ----------
  frigate:
    image: ghcr.io/blakeblackshear/frigate:stable
    container_name: frigate
    restart: unless-stopped
    privileged: true               # required for GPU access
    devices:
      - /dev/dri/card0               # Intel iGPU; replace with /dev/nvidia0 for Nvidia
    ports:
      - "5000:5000"                  # UI
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /srv/ssd/frigate-config:/config
      - /srv/hdd/frigate-recordings:/media
      - /dev/bus/usb:/dev/bus/usb   # optional USB cams
    environment:
      - FRIGATE_RTSP_PASSWORD=camPass!
    networks:
      - internal

  # ---------- Reverse Proxy (Traefik) ----------
  traefik:
    image: traefik:v3.0
    container_name: traefik
    command:
      - "--api.insecure=true"
      - "--providers.docker=true"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.myresolver.acme.tlschallenge=true"
      - "--certificatesresolvers.myresolver.acme.email=you@example.com"
      - "--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080"   # Traefik dashboard
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - traefik-letsencrypt:/letsencrypt
    networks:
      - internal

networks:
  internal:
    driver: bridge

volumes:
  traefik-letsencrypt:
```

**Key Community Hacks embedded:**

* **`privileged: true`** for Frigate GPU passthrough – a frequent stumbling block for newcomers.  
* **Separate SSD/HDD mounts** to keep DB latency low.  
* **Traefik** acts as a single TLS terminator, eliminating port conflicts that plagued early threads.  

Deploy:

```bash
docker compose up -d
```

### 4. Fine‑Tune Resource Limits  

Add these lines under each service (example for Seafile) to prevent one container from hogging RAM:

```yaml
    deploy:
      resources:
        limits:
          memory: 2g
        reservations:
          memory: 1g
```

Community members who omitted limits reported *“Out‑of‑memory killer (OOM) killed Seafile”* during large syncs.

### 5. Set Up Monitoring  

Create a minimal Prometheus + Grafana stack (the community’s preferred observability combo):

```yaml
# Add to the same docker‑compose.yml
  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    networks:
      - internal

  grafana:
    image: grafana/grafana-oss:latest
    ports:
      - "3001:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=GrafanaPass!
    depends_on:
      - prometheus
    networks:
      - internal
```

`prometheus.yml` (save beside the compose file):

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'docker'
    static_configs:
      - targets: ['host.docker.internal:9323']  # docker stats exporter
```

Install the Docker stats exporter:

```bash
docker run -d --name cadvisor \
  --publish=9323:8080 \
  --volume=/var/run/docker.sock:/var/run/docker.sock:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  google/cadvisor:latest
```

Now you can visualise CPU, memory, and network usage per container, a practice that **prevented dozens of “Frigate frame‑drop” incidents** in the subreddit.

### 6. Backup & Disaster Recovery  

#### a. Daily rsync snapshots (SSD volumes)

```bash
#!/bin/bash
BACKUP_ROOT=/backup/$(date +%F)
mkdir -p $BACKUP_ROOT

# Seafile & Immich DB
rsync -a --delete /srv/ssd/seafile $BACKUP_ROOT/seafile
rsync -a --delete /srv/ssd/immich-db $BACKUP_ROOT/immich-db

# Frigate config (small)
rsync -a /srv/ssd/frigate-config $BACKUP_ROOT/frigate-config
```

Schedule with `crontab -e`:

```
0 2 * * * /usr/local/bin/daily_backup.sh >> /var/log/backup.log 2>&1
```

#### b. Off‑site sync (Wasabi, Backblaze B2, or any S3‑compatible)

```bash
rclone sync /backup remote:my-selfhosted-backups --log-file=/var/log/rclone.log
```

Community users who skipped the off‑site copy lost weeks of video footage after a single HDD failure—hence the dual‑layer backup is now *standard*.

### 7. Security Hardening Checklist  

| Action | Command / Setting | Reason |
|--------|-------------------|--------|
| **Run containers as non‑root** | `user: 1000` in compose (most images already do) | Limits impact of a compromised container |
| **Enable Docker seccomp** | `"security_opt": ["seccomp=./seccomp-profile.json"]` | Blocks syscalls not needed by Seafile/Immich |
| **Restrict API tokens** | Store secrets in `~/.config/vault` and reference via `${SECRETS_PATH}` | Prevents accidental Git history leakage |
| **2FA on host login** | `sudo apt install libpam-google-authenticator` | Adds an extra trust layer for SSH |
| **Automatic TLS** | Traefik `certificatesresolvers.myresolver.acme.email` | No manual cert management, reduces human error |

---

## Pros & Cons / Comparative Table  

| App | Primary Use‑Case | Resource Profile | Strengths (Community‑Validated) | Weaknesses |
|-----|-------------------|------------------|---------------------------------|------------|
| **Seafile** | File sync & collaboration | Moderate CPU, low GPU | Fast delta sync, fine‑grained ACLs, good Docker image | UI feels dated, limited native mobile clients |
| **Immich** | Photo & video library with AI tagging | High storage I/O, moderate RAM | Modern React UI, built‑in facial recognition, EXIF handling | Still maturing; occasional DB migrations |
| **Frigate** | Real‑time NVR with object detection | Heavy GPU (or high‑end CPU) | Edge‑AI detection, MQTT integration, low latency | Complex GPU passthrough, higher power draw |
| **Traefik** | Reverse proxy + TLS automation | Light | Auto‑discovers Docker containers, integrates with LetsEncrypt | Debugging routing errors can be