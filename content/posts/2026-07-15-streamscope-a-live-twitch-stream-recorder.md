---
title: "Streamscope Deep Dive: How to Self‑Host a Reliable Live Twitch Stream Recorder (2026 Guide)"
date: 2026-07-15T01:22:05+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Learn to install, configure, and run Streamscope on a VPS, compare alternatives, and get expert tips from the r/selfhosted community."
---

## The Community Spark  

The **r/selfhosted** subreddit lit up last week when a user posted *“Just got Streamscope working on my cheap VPS—finally a way to archive Twitch streams without third‑party services!”* Within minutes the thread exploded with questions, tweaks, and success stories. The core problem? **Twitch’s “download‑only‑after‑broadcast” policy** pushes creators and archivists toward either expensive cloud services or fragile, script‑based hacks. Streamscope promises a *live* recorder that runs 24/7 on modest hardware, but the community quickly realized that “plug‑and‑play” is a myth—configuration, storage, and API limits are real obstacles.

If you’re reading this, you’re probably:

* A content creator who wants a personal backup of every broadcast.  
* An archivist looking to preserve niche streams for research.  
* A sysadmin curious about running a low‑cost, self‑hosted media service.

Below is the distilled wisdom from the Reddit thread, plus the technical roadmap that turned curiosity into a production‑grade deployment.

---

## Synthesized Community Perspectives  

| Community Voice | Key Takeaway | Why It Matters |
|-----------------|--------------|----------------|
| **“I got it running on a 1 vCPU, 1 GB RAM droplet.”** – *u/lowenddev* | Streamscope’s Docker image is lightweight; a cheap VPS suffices for a single‑channel recorder. | Cost‑effectiveness – you don’t need a beefy server unless you plan multi‑stream capture. |
| **“The Twitch API rate‑limit killed my cron job.”** – *u/api‑guru* | Respect Twitch’s 30‑request‑per‑minute per token limit; implement token refresh and back‑off logic. | Prevents bans and ensures continuous recording. |
| **“I switched to SQLite → PostgreSQL for reliability.”** – *u/db‑master* | Default SQLite works for hobbyists; PostgreSQL is recommended for multi‑user setups or when you need transactional guarantees. | Guarantees data integrity under heavy load. |
| **“FFmpeg crashes on 1080p 60fps streams.”** – *u/ffmpeg‑ninja* | Pin FFmpeg to version 5.1.3 and add `-max_muxing_queue_size 9999`. | Stabilizes high‑resolution recordings. |
| **“Mount a separate volume for recordings; otherwise the OS runs out of space.”** – *u/storage‑sage* | Use a bind‑mount or dedicated block storage (e.g., LVM or ZFS). | Prevents silent data loss and keeps the container lightweight. |

**Consensus:** Streamscope is *doable* on modest hardware, but production reliability hinges on three pillars—**API compliance, storage planning, and video pipeline stability**. The disagreements centered around the choice of database and video codec tweaks; both have solid arguments depending on scale.

---

## Deep‑Dive Actionable Guide  

Below is a **step‑by‑step tutorial** that incorporates the community’s hard‑won lessons. The guide assumes a fresh Ubuntu 22.04 LTS VPS (or any Debian‑based distro) with `sudo` privileges.

### 1️⃣ Prerequisites  

| Item | Recommended Version |
|------|----------------------|
| Docker Engine | 24.0+ |
| Docker Compose | 2.23+ |
| FFmpeg | 5.1.3 (or later) |
| PostgreSQL (optional) | 15 |
| Twitch OAuth token | generated via <https://dev.twitch.tv/console> |

> **Tip:** If you’re on a low‑end droplet, stick with SQLite (bundled) to avoid the extra PostgreSQL overhead.

### 2️⃣ Install Docker & Docker Compose  

```bash
# Update & install prerequisites
sudo apt-get update && sudo apt-get install -y \
    ca-certificates curl gnupg lsb-release

# Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Set up the stable repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine and Compose plugin
sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Verify
docker version
docker compose version
```

### 3️⃣ Create a Dedicated Storage Volume  

```bash
# Example using a separate disk /dev/vdb (adjust to your provider)
sudo mkfs.ext4 /dev/vdb
sudo mkdir -p /mnt/streamscope
sudo mount /dev/vdb /mnt/streamscope
# Persist across reboots
echo '/dev/vdb /mnt/streamscope ext4 defaults 0 2' | sudo tee -a /etc/fstab
```

> **Why?** A dedicated mount point prevents the root filesystem from filling up, which would otherwise cause Docker to abort silently.

### 4️⃣ Pull the Official Streamscope Docker Image  

```bash
docker pull ghcr.io/streamscope/streamscope:latest
```

> The image is built on Alpine 3.18, keeping it under 150 MB.

### 5️⃣ Configure Environment Variables  

Create a `.env` file in a safe location (e.g., `/home/ubuntu/streamscope/.env`):

```dotenv
# Twitch credentials – keep these secret!
TWITCH_CLIENT_ID=your_client_id
TWITCH_CLIENT_SECRET=your_client_secret
TWITCH_OAUTH_TOKEN=oauth:your_generated_token

# Recording settings
RECORDING_PATH=/recordings
MAX_CONCURRENT=3                # Max simultaneous streams
RESOLUTION=1080p                # Options: 720p, 1080p, 1440p
FRAMERATE=60                    # 30 or 60

# Database (choose ONE)
# SQLite (default, no extra vars)
# PostgreSQL example:
POSTGRES_HOST=postgres
POSTGRES_DB=streamscope
POSTGRES_USER=ss_user
POSTGRES_PASSWORD=strongpassword
```

### 6️⃣ Docker Compose Stack  

Create `docker-compose.yml` next to the `.env` file:

```yaml
version: "3.9"

services:
  streamscope:
    image: ghcr.io/streamscope/streamscope:latest
    container_name: streamscope
    restart: unless-stopped
    env_file:
      - ./.env
    volumes:
      - /mnt/streamscope/recordings:${RECORDING_PATH}
      - ./config:/app/config   # optional custom config files
    depends_on:
      - postgres   # only if you enabled PostgreSQL

  # Optional PostgreSQL service
  postgres:
    image: postgres:15-alpine
    restart: unless-stopped
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - pg_data:/var/lib/postgresql/data

volumes:
  pg_data:
```

> **Note:** If you stay with SQLite, simply delete the `postgres` service and the `depends_on` line.

### 7️⃣ Start the Stack  

```bash
docker compose up -d
docker compose logs -f streamscope   # watch startup messages
```

You should see something like:

```
2026-07-14T22:31:12.345Z INFO Starting Streamscope v2.1.0
2026-07-14T22:31:12.350Z INFO Authenticated with Twitch (rate limit: 30 req/min)
2026-07-14T22:31:12.360Z INFO Listening for channel updates…
```

### 8️⃣ Adding Channels to Record  

Streamscope ships with a simple REST API (exposed on port 8080). You can add a channel via `curl`:

```bash
curl -X POST http://localhost:8080/api/v1/channels \
  -H "Authorization: Bearer ${TWITCH_OAUTH_TOKEN}" \
  -d '{"login":"example_channel","quality":"1080p60"}'
```

Alternatively, edit `config/channels.yaml` (mounted at `./config`) and reload:

```yaml
channels:
  - login: example_channel
    quality: 1080p60
  - login: another_streamer
    quality: 720p30
```

Then restart:

```bash
docker compose restart streamscope
```

### 9️⃣ Handling Twitch API Rate Limits  

Streamscope includes built‑in back‑off, but the community recommends **spreading channel checks** across the minute. Add a small delay in `config/settings.yaml`:

```yaml
api:
  request_interval_seconds: 2   # default is 1
```

For heavy multi‑channel setups (>20), consider creating **multiple Twitch apps** each with its own token and load‑balance them via `api.tokens` list.

### 🔟 Monitoring & Alerts  

- **Healthcheck**: Docker automatically polls `/healthz`.  
- **Prometheus Exporter**: Enable `METRICS_ENABLED=true` in `.env` and scrape `http://localhost:9090/metrics`.  
- **Log Rotation**: Add a `logrotate` config to avoid giant Docker logs.

```conf
/var/lib/docker/containers/*/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
    copytruncate
}
```

---

## Pros & Cons / Comparative Table  

| Feature | Streamscope | Twitch Leecher | Streamlink (record) | yt-dlp (twitch) |
|---------|-------------|----------------|---------------------|-----------------|
| **Live Recording** | ✅ (captures as stream happens) | ❌ (post‑broadcast download) | ✅ (requires manual script) | ✅ (needs `--twitch` flag) |
| **Self‑Hosted** | ✅ (Docker) | ❌ (desktop app) | ✅ (CLI) | ✅ (CLI) |
| **Multi‑Channel Support** | ✅ (up to dozens, DB‑backed) | ✅ (single channel per session) | ✅ (scriptable) | ✅ (scriptable) |
| **API Rate‑Limit Handling** | Built‑in back‑off | N/A | Manual handling | Manual handling |
| **Storage Management** | Bind‑mount + config | User‑managed | User‑managed | User‑managed |
| **Web UI** | ✅ (built‑in dashboard) | ❌ | ❌ | ❌ |
| **Ease of Setup** | Medium (Docker + env) | Easy (GUI) | Hard (shell) | Medium (CLI) |
| **Community Support (2026)** | Growing Reddit + GitHub | Stagnant | Small | Active |
| **Cost** | Low (VPS $5/mo) | Free (desktop) | Free | Free |
| **Scalability** | High (K8s ready) | Low | Medium | Medium |

**Bottom line:** If you need *continuous, multi‑channel, live* archiving with a web UI, Streamscope wins. For one‑off VOD downloads, Twitch Leecher remains the simplest.

---

## The Verdict / Expert Advice  

| Persona | Recommendation |
|---------|----------------|
| **Hobbyist streamer (single channel, <$5/mo VPS)** | Deploy the **SQLite‑based Docker compose** as shown. No extra DB needed. |
| **Small archive team (5‑10 channels, need reliability)** | Use **PostgreSQL**, enable metrics, and set up a **systemd timer** to rotate old recordings (e.g., keep 30 days). |
| **Research lab / large‑scale archivist (20+ channels, compliance)** | Deploy **Streamscope on a Kubernetes cluster**, use multiple Twitch client IDs, and store recordings on a **CephFS** or **S3‑compatible bucket** for redundancy. |
| **Non‑technical user** | Consider a managed VPS with a one‑click Docker installer (many providers now offer “Streamscope‑Ready” images) or fall back to Twitch Leecher for on‑demand VODs. |

**Key takeaways:**  

1. **Respect Twitch limits** – token rotation & request spacing are non‑negotiable.  
2. **Separate storage** – bind‑mount a dedicated volume, prune old files automatically.  
3. **Monitor** – Prometheus + Grafana dashboards give early