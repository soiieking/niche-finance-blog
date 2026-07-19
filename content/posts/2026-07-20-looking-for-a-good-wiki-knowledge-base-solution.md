---
title: "Best Self‑Hosted Wiki & Knowledge Base 2026: Community‑Tested Guide & Setup"
date: 2026-07-20T07:25:09+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Discover the top self‑hosted wiki solutions, real‑world Reddit insights, step‑by‑step Docker installs, and which tool fits your team."
---

## The Community Spark  

The r/selfhosted thread *“Looking for a good wiki / knowledge base solution”* exploded this week, with over 2 500 up‑votes. Members are tired of SaaS lock‑ins, data‑privacy worries, and the steep learning curve of classic MediaWiki. They want a modern, searchable, markdown‑friendly knowledge base they can run on a modest VPS or a home server—preferably with Docker support and an easy backup strategy.

## Synthesized Community Perspectives  

| What users **agreed** on | What sparked **debate** |
|--------------------------|--------------------------|
| **Docker first** – most contributors installed via Docker Compose for reproducibility. | **Feature set vs. simplicity** – power users gravitated toward Wiki.js, while newcomers liked BookStack’s UI. |
| **Markdown is king** – any solution that natively renders markdown won favor. | **Self‑hosting complexity** – some argued a full‑stack MediaWiki is overkill, others insisted on its mature extensions. |
| **Backup & migration** – built‑in DB dumps and volume snapshots are non‑negotiable. | **Resource footprint** – tiny DokuWiki vs. heavier Wiki.js with Node.js runtime. |

The consensus: **Wiki.js** and **BookStack** are the top two choices for most self‑hosters, with **DokuWiki** as a lightweight fallback and **MediaWiki** reserved for those needing massive extensions (e.g., Semantic MediaWiki).

## Deep‑Dive Actionable Guide  

Below is a **single‑command Docker‑Compose** setup that works on any Debian‑based VPS (Ubuntu, Debian, Raspberry Pi OS). Adjust the `environment` block for your domain and SMTP.

### 1. Prepare the host  

```bash
# Update and install Docker + Compose
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install -y docker.io docker-compose
sudo usermod -aG docker $USER   # log out/in afterwards
```

### 2. Create a project folder  

```bash
mkdir -p ~/wiki/wikijs && cd ~/wiki/wikijs
```

### 3. Docker‑Compose file (Wiki.js)  

```yaml
# wikijs/docker-compose.yml
version: "3.8"
services:
  db:
    image: postgres:15-alpine
    restart: unless-stopped
    environment:
      POSTGRES_DB: wiki
      POSTGRES_USER: wiki
      POSTGRES_PASSWORD: ${WIKI_DB_PASS}
    volumes:
      - db_data:/var/lib/postgresql/data

  wiki:
    image: requarks/wiki:2
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wiki
      DB_PASS: ${WIKI_DB_PASS}
      DB_NAME: wiki
    depends_on:
      - db
    volumes:
      - ./data:/wiki/data

volumes:
  db_data:
```

Create a `.env` file for secrets:

```bash
echo "WIKI_DB_PASS=$(openssl rand -hex 16)" > .env
```

### 4. Launch  

```bash
docker compose up -d
```

Visit `http://YOUR_IP:3000` and finish the web installer (admin user, site name, optional SSL termination with Caddy/Nginx).

### 5. Backup Strategy  

```bash
# Daily DB dump
0 2 * * * docker exec wiki-db pg_dump -U wiki -d wiki > /backups/wiki_$(date +\%F).sql

# Volume snapshot (requires LVM or ZFS; example with rsync)
0 3 * * * rsync -a ~/wiki/wikijs/data/ ~/backups/wiki_data_$(date +\%F)/
```

### 6. Quick Switch to BookStack (if you prefer Laravel/PHP)

```bash
mkdir -p ~/wiki/bookstack && cd ~/wiki/bookstack
cat > docker-compose.yml <<'EOF'
version: "3.8"
services:
  db:
    image: mariadb:10.11
    restart: unless-stopped
    environment:
      MYSQL_DATABASE: bookstack
      MYSQL_USER: bookstack
      MYSQL_PASSWORD: ${BS_DB_PASS}
      MYSQL_ROOT_PASSWORD: ${BS_ROOT_PASS}
    volumes:
      - db_data:/var/lib/mysql

  app:
    image: linuxserver/bookstack
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      DB_HOST: db
      DB_DATABASE: bookstack
      DB_USERNAME: bookstack
      DB_PASSWORD: ${BS_DB_PASS}
    depends_on:
      - db
    volumes:
      - ./config:/config

volumes:
  db_data:
EOF

echo "BS_DB_PASS=$(openssl rand -hex 16)" > .env
echo "BS_ROOT_PASS=$(openssl rand -hex 16)" >> .env
docker compose up -d
```

BookStack now runs at `http://YOUR_IP:8080`. Its UI feels like a modern documentation portal with WYSIWYG editing, while Wiki.js offers a richer plugin ecosystem.

## Pros & Cons Comparison  

| Feature | Wiki.js | BookStack | DokuWiki | MediaWiki |
|---------|---------|-----------|----------|-----------|
| **Markdown native** | ✅ | ✅ (via editor) | ❌ (requires plugin) | ❌ (requires extension) |
| **Docker ready** | ✅ (official image) | ✅ (LinuxServer.io) | ✅ (community) | ✅ (official) |
| **Resource usage** | Medium (Node.js) | Medium (PHP) | Low (PHP, no DB) | High (MySQL + many extensions) |
| **Extensibility** | High (plugins, GraphQL) | Moderate (hooks) | Low | Very High (thousands of extensions) |
| **Authentication** | LDAP, OAuth2, SSO | LDAP, SSO | Simple auth | LDAP, OAuth, etc. |
| **Search** | Elastic/Meili (built‑in) | MySQL full‑text | Full‑text (optional) | Elastic (via extensions) |
| **Learning curve** | Moderate | Low | Very low | Steep |
| **Best for** | Teams needing API & custom UI | Small‑to‑mid teams preferring WYSIWYG | Minimalist docs on low‑end hardware | Large wikis, community projects |

## The Verdict – Which Wiki Fits Your Persona?  

| Persona | Recommended Solution | Why |
|---------|----------------------|-----|
| **DevOps / API‑first team** | **Wiki.js** | GraphQL API, markdown, modern UI, extensible plugins. |
| **Non‑tech writers & SMEs** | **BookStack** | WYSIWYG editor, simple Docker setup, intuitive navigation. |
| **Home lab hobbyist** | **DokuWiki** | No DB, tiny footprint, easy to back up as plain files. |
| **Open‑source community / massive documentation** | **MediaWiki** | Proven scalability, massive extension ecosystem. |

If you’re unsure, spin up both Wiki.js and BookStack on the same VPS (different ports) and let the team trial them for a week. The community consensus leans heavily toward Wiki.js for its API power, but BookStack wins on ease‑of‑use.

---

## Frequently Asked Questions  

**Q1: Can I run a wiki behind a reverse proxy with HTTPS?**  
Yes. Place an Nginx or Caddy container in front of the wiki service, terminate TLS with Let’s Encrypt, and forward traffic to the internal port (3000 for Wiki.js, 8080 for BookStack).

**Q2: How do I migrate from an existing MediaWiki to Wiki.js?**  
Export MediaWiki pages as XML, then use Wiki.js’s *Import* tool (supports Markdown, HTML, and MediaWiki wikitext). Scripts are available on the Wiki.js GitHub repo to automate bulk conversion.

**Q3: What is the minimal VPS spec for a production Wiki.js instance?**  
A 2 vCPU, 2 GB RAM VPS with 20 GB SSD is comfortable for up to 200 active users. For larger teams, bump to 4 vCPU/4 GB RAM and enable MeiliSearch for faster full‑text search.

**Q4: Are backups truly “zero‑downtime”?**  
When using Docker volumes, `docker exec` `pg_dump` (for Wiki.js) or `mysqldump` (for BookStack) runs without stopping the container, giving you consistent dumps. Pair this with daily rsync of the data volume for full protection.

---

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Can I run a wiki behind a reverse proxy with HTTPS?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. Place an Nginx or Caddy container in front of the wiki service, terminate TLS with Let’s Encrypt, and forward traffic to the internal port (3000 for Wiki.js, 8080 for BookStack)."
      }
    },
    {
      "@type": "Question",
      "name": "How do I migrate from an existing MediaWiki to Wiki.js?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Export MediaWiki pages as XML, then use Wiki.js’s Import tool (supports Markdown, HTML, and MediaWiki wikitext). Scripts on the Wiki.js GitHub repository can automate bulk conversion."
      }
    },
    {
      "@type": "Question",
      "name": "What is the minimal VPS spec for a production Wiki.js instance?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "A 2 vCPU, 2 GB RAM VPS with 20 GB SSD comfortably serves up to 200 active users. Larger teams should consider 4 vCPU/4 GB RAM and enable MeiliSearch for faster search."
      }
    },
    {
      "@type": "Question",
      "name": "Are backups truly “zero‑downtime”?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "When using Docker volumes, docker exec pg_dump (for Wiki.js) or mysqldump (for BookStack) runs without stopping the container, giving you consistent dumps. Pair this with daily rsync of the data volume for full protection."
      }
    }
  ]
}
</script>