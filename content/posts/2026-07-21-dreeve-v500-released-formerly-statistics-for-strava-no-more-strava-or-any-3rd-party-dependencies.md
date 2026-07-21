---
title: "Dreeve v5.0 Review: Total Data Sovereignty with Zero Third-Party Dependencies"
date: 2026-07-21T11:30:39+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Dreeve v5 drops all third-party APIs for complete offline activity tracking. We analyze the r/selfhosted hype, migration steps, and why this is the ultimate data sovereignty upgrade for cyclists."
---

## The Community Spark: Why r/selfhosted Is Celebrating Dreeve v5

The r/selfhosted community has been abuzz following the release of **Dreeve v5.0.0**, formerly known as "Statistics for Strava." This isn't just a version bump; it's a philosophical shift. The headline feature? **No more Strava or third-party dependencies.**

For years, self-hosters struggled with OAuth token expirations, API rate limits, and the constant threat of vendor lock-in. Dreeve v5 eliminates these pain points by operating entirely on local data, turning your server into a private, immutable fitness archive. This update resonates deeply with the community's core values: privacy, control, and perpetuity.

## Synthesized Community Perspectives

Aggregating feedback from the trending discussion, the consensus is overwhelmingly positive, with a few nuanced debates:

*   **The "Set and Forget" Victory:** Users agree that removing API dependencies solves the #1 complaint of v4. No more scripts failing weekly due to revoked tokens. Your data lives where you control it.
*   **Migration Anxiety vs. Reward:** Some veteran Strava enthusiasts worry about the initial import friction. However, community consensus suggests that the "one-time import" effort pays immediate dividends in long-term stability.
*   **Privacy Purists:** Many users are migrating solely to escape correlation tracking. Dreeve v5 is now championed as the gold standard for fitness data that never leaves your LAN or VPS.

## Deep-Dive: Deploying Dreeve v5.0

Dreeve v5 is designed for low-maintenance deployment. Since it relies on local files, your configuration focuses on storage volume management rather than API keys.

### Prerequisites
*   Access to a Linux server/VPS or local machine.
*   Docker & Docker Compose installed.
*   Existing `.gpx`, `.tcx`, or `.fit` activity files.

### Docker Compose Setup

Create a `docker-compose.yml` file. Note the persistent volume for your data directory.

```yaml
version: "3.8"
services:
  dreeve:
    image: ghcr.io/kocsis_ary/dreeve:latest
    container_name: dreeve_v5
    restart: unless-stopped
    ports:
      - "8080:8080" # Access via http://your-ip:8080
    volumes:
      - ./dreeve-data:/app/data  # Critical: Map your activity files here
      - ./dreeve-config:/app/config
    environment:
      - TZ=UTC
```

### Actionable Migration Tip
To migrate from v4 or Strava:
1.  Export your activities from Strava (or previous Dreeve instances) as a ZIP of raw files.
2.  Extract and place the GPX/TCX files directly into the `./dreeve-data` directory mapped above.
3.  Start the container. Dreeve v5 will auto-index files upon boot without any external authentication.

## Comparative Analysis: v4 API vs. v5 Local

| Feature | Dreeve v4 (Strava API) | Dreeve v5 (Local Only) | Strava Web |
| :--- | :---: | :---: | :---: |
| **3rd Party Dependencies** | Strava API | **None** | Strava |
| **Data Privacy** | Moderate | **High