---
title: "Anchor v0.14.0 Review: How to Migrate from Google Keep to Self-Hosted Notes"
date: 2026-07-19T23:14:11+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Master the Anchor v0.14.0 Google Keep import with our community-verified setup guide, Docker configs, and expert migration strategy for self-hosted note-taking."
---

## The Community Spark
The r/selfhosted community erupted when Anchor v0.14.0 dropped, primarily because it finally cracked the Google Keep migration problem. For years, privacy-conscious users stuck with Keep due to the painful export format: a scattered folder of HTML files, nested image directories, and broken relative links. Anchor’s native importer promised to parse, restructure, and preserve that data into a clean, queryable SQLite or PostgreSQL database—all while keeping your VPS stack lean. The core question dominating the thread: *Does the import actually work out-of-the-box, or is it a formatting nightmare?*

## What r/selfhosted Actually Thinks
Synthesizing three weeks of developer discussions, the consensus leans heavily positive, with clear caveats:
- **The Win:** The Python/FastAPI backend handles Keep’s quirky rich-text HTML remarkably well. Nested lists and checkmarks convert to markdown faithfully.
- **The Debate:** Several sysadmins flagged that Keep’s “labels” map to Anchor’s `tags` field, but multi-label notes sometimes duplicate tags due to how Google’s JSON export structures arrays. A quick `python manage.py dedupe_tags` script shared in the comments fixes this instantly.
- **Storage Reality:** Keep images are base64-encoded in older exports. Anchor’s v0.14.0 importer now strips and saves them as optimized WebPs in a local `./media/assets/` directory. Users running low-disk VPS plans (8GB) should mount an external data volume or enable S3-compatible object storage during setup.
- **Trust Factor:** The open-source license is AGPLv3, and the maintainer actively merges PRs. Community trust is high, but long-term sync client stability remains the usual self-hosted wildcard.

## Step-by-Step: Deploying Anchor & Running the Keep Import
Here is the verified, production-ready workflow used by r/selfhosted veterans:

1. **Pull & Configure Docker Compose**
```yaml
version: '3.8'
services:
  anchor:
    image: getanchor/anchor:0.14.0
    restart: unless-stopped
    ports:
      - "8080:8000"
    volumes:
      - ./data:/app/data
      - ./uploads:/app/uploads
    environment:
      - DATABASE_URL=sqlite:///./data/anchor.db
      - SECRET_KEY=your-ultra-secure-key-here
      - ALLOWED_ORIGINS=https://notes.yourdomain.com
```

2. **Run the One-Shot Import Container**
Mount your exported Google Keep `KeepExport` directory as a read-only volume. The importer runs independently to prevent blocking the main FastAPI server:
```bash
docker run --rm \
  -v /path/to