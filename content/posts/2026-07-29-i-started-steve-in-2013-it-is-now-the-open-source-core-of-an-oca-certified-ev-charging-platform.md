---
title: 'How SteVe EVolved: From 2013 GitHub Project to OCA-Certified Charging Platform'
date: '2026-07-29T18:48:23+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding How SteVe EVolved: From 2013 GitHub Project to OCA-Certified
  Charging Platform.'
---

## The Community Spark
When a developer recently took to Reddit's r/selfhosted community to announce that their 2013 project, SteVe, is now the open-source core of an OCA-certified EV charging platform, the community took notice. EV adoption has exploded, and with it, a growing demand for self-hosted, privacy-respecting Charge Point Management Systems (CPMS). 
The core issue? Most commercial EV charging software is expensive, proprietary, and relies on cloud infrastructure that can be deprecated at any time. The community consensus was clear: why pay exorbitant SaaS fees when you can host your own OCPP (Open Charge Point Protocol) server?
## Synthesized Community Perspectives
The r/selfhosted thread highlighted a fascinating intersection of EV enthusiasts and homelab veterans. Here is what the community agreed on, alongside the debates:
- **Consensus**: Users universally praised SteVe for its lightweight footprint. Unlike heavy commercial platforms, SteVe runs flawlessly on modest hardware, making it perfect for a Raspberry Pi or a basic VPS. It uses the standard OCPP 1.6 protocol, ensuring compatibility with a vast array of commercial and residential EV chargers.
- **Debate**: The main technical debate centered around whether to run SteVe bare-metal via a JAR file or within a Docker container. Docker proponents argued for easier database (H2/MySQL) swaps and clean backups, while bare-metal advocates preferred direct performance for single-station home setups.
- **Counter-Argument**: A few users pointed out that SteVe *currently* focuses strictly on OCPP 1.6, lacking native OCPP 2.0.1 support. However, the developer noted that its modular architecture has allowed commercial entities to extend SteVe easily for newer protocols and OCA (Open Charge Alliance) certification compliance.
## Deep-Dive Actionable Guide: Self-Hosting SteVe via Docker
To help you leverage this powerful OCPP backend, here is a concise, community-tested guide to deploying SteVe on a Linux VPS using Docker.
### Prerequisites
- A Linux VPS or local server with Docker and Docker Compose installed.
- An EV charger supporting OCPP 1.6 via WebSocket (e.g., OpenEVSE or Wallbox).
### Step 1: Directory Setup
Create a workspace for your SteVe deployment.
```bash
mkdir ~/steve && cd ~/steve
mkdir data config
```
### Step 2: Docker Compose Configuration
Create a `docker-compose.yml` file to spin up both SteVe and a MariaDB database.
```yaml
version: '3.8'
services:
  mariadb:
    image: mariadb:10.6
    container_name: steve_db
    environment:
      MYSQL_ROOT_PASSWORD: supersecretpassword
      MYSQL_DATABASE: stevedb
      MYSQL_USER: steve
      MYSQL_PASSWORD: stevepassword
    volumes:
      - ./data/mysql:/var/lib/mysql
    restart: unless-stopped
  steve:
    image: rwth-iat/steve:latest
    container_name: steve_app
    depends_on:
      - mariadb
    ports:
      - "8180:8180" # SteVe Web UI & API
    environment:
      - JAVA_OPTS=-Dspring.profiles.active=mysql
      - SPRING_DATASOURCE_URL=jdbc:mysql://mariadb:3306/stevedb
      - SPRING_DATASOURCE_USERNAME=steve
      - SPRING_DATASOURCE_PASSWORD=stevepassword
    restart: unless-stopped
```
### Step 3: Deploy and Connect
Launch the stack and connect your charger.
```bash
docker-compose up -d
docker-compose logs -f
```
Configure your EV charger's OCPP backend URL to: `ws://<YOUR_SERVER_IP>:8180/steve/websocket/CentralSystemService`
Log into the SteVe UI at `http://<YOUR_SERVER_IP>:8180/steve/manager` using the default credentials (`admin` / `admin`) to manage your charge points.
![pshrsK.png](//tcdn.ch Civit.ai/api/v1/images?src=download&id=126496)
## Pros & Cons: SteVe vs. Commercial CPMS
| Feature | Self-Hosted SteVe | Commercial SaaS (e.g. ChargePoint) |
| :--- | :--- | :--- |
| **Cost** | Free (minimal hosting cost) | High monthly fees per station |
| **Privacy** | 100% local data retention | Vendor-controlled cloud |
| **Customization** | Fully open-source, high extensibility | Locked ecosystem |
| **Protocol Support** | OCPP 1.6 (extensive) | 1.6, 2.0.1, ISO 15118 |
| **OCA Certification** | Capable via commercial forks | Fully certified out-of-the-box |
## The Verdict / Expert Advice
For a homeowner with a compatible EV charger who wants local control, charging history, and rate management without monthly fees, deploying SteVe on a VPS or local server is a clear winner. It is a prime example of the self-hosted ethos. 
If you are a commercial operator seeking to build a compliant network, leveraging the open-source core of SteVe to build a custom CPMS offers massive cost savings over proprietary licenses, provided you invest the engineering hours to achieve full OCA certification and implement isolated billing systems.
## Frequently Asked Questions (FAQ)
### What is SteVe EV software used for?
SteVe acts as a Charge Point Management System (CPMS) that communicates with EV chargers via the Open Charge Point Protocol (OCPP). It handles charger authorization, billing, power management, and telemetry logging.
### Can SteVe run on a Raspberry Pi?
Yes. Because SteVe is optimized for Java environments, it runs efficiently on low-power ARM devices like a Raspberry Pi. However, it is recommended to pair it with a lighter database or ensure adequate RAM (at least 2GB) for optimal performance.
### Does SteVe support OCPP 2.0.1?
The open-source base primarily supports OCPP 1.6, which remains the industry standard for most installations. However, its modular architecture allows developers to extend functionality to OCPP 2.0.1 for advanced features.
### Is self-hosting an EV charger safe?
Yes. By hosting the OCPP backend on your own VPS behind a HTTPS reverse proxy, you retain complete control over your charging data and eliminate risks associated with third-party cloud outages.
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is SteVe EV software used for?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "SteVe acts as a Charge Point Management System (CPMS) that communicates with EV chargers via the Open Charge Point Protocol (OCPP). It handles charger authorization, billing, power management, and telemetry logging."
      }
    },
    {
      "@type": "Question",
      "name": "Can SteVe run on a Raspberry Pi?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. Because SteVe is optimized for Java environments, it runs efficiently on low-power ARM devices like a Raspberry Pi. However, it is recommended to pair it with a lighter database or ensure adequate RAM (at least 2GB) for optimal performance."
      }
    },
    {
      "@type": "Question",
      "name": "Does SteVe support OCPP 2.0.1?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "The open-source base primarily supports OCPP 1.6, which remains the industry standard for most installations. However, its modular architecture allows developers to extend functionality to OCPP 2.0.1 for advanced features."
      }
    },
    {
      "@type": "Question",
      "name": "Is self-hosting an EV charger safe?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. By hosting the OCPP backend on your own VPS behind a HTTPS reverse proxy, you retain complete control over your charging data and eliminate risks associated with third-party cloud outages."
      }
    }
  ]
}
</script>
