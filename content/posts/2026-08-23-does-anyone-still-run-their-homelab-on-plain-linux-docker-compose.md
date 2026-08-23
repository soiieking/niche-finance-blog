## The Plain Truth About Homelabs

I've been lurking on r/selfhosted for a while now, and one question keeps popping up: does anyone still run their homelab on plain Linux + Docker Compose? I mean, I get it – simplicity is appealing, but is it still viable in 2023?

I'm not here to bash plain Linux + Docker Compose. I've built and broken things with this combo, and it's a great way to get started. But let's be real – it's not the most efficient way to manage a homelab, especially when you're dealing with multiple services.

### Step 1: Choose Your Linux Distribution

You can't go wrong with Ubuntu Server 22.04 LTS. It's a solid choice for most homelab setups. I mean, you can use Arch Linux or Fedora if you want to, but why make things harder on yourself?

```bash
sudo apt update && sudo apt upgrade -y
```

### Step 2: Install Docker and Docker Compose

This is the part where most people would recommend using Podman instead of Docker. And hey, I love Podman – it's faster and more secure. But if you're already invested in the Docker ecosystem, it's not worth switching.

```bash
sudo apt install docker.io docker-compose -y
```

### Step 3: Set Up Your Homelab

This is where things get interesting. You can use a single Docker Compose file to manage all your services, or you can use separate files for each service. It's up to you.

Here's an example of a simple Docker Compose file for a Nextcloud instance:
```yaml
---
title: "Does anyone still run their homelab on plain Linux + Docker Compose ?"
date: 2026-08-23T18:00:11+08:00
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "A community-focused analysis exploring the recent discussions and practical insights regarding Does anyone still run their homelab on plain Linux + Docker Compose ?."
version: '3'
services:
  nextcloud:
    image: nextcloud:latest
    ports:
      - 8080:80
    environment:
      - MYSQL_ROOT_PASSWORD=my_secret_password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=my_user
      - MYSQL_PASSWORD=my_password
    volumes:
      - /path/to/data:/var/www/html
```

### Step 4: Configure Your Services

This is the part where most people would recommend using a more advanced orchestration tool like Kubernetes or Rancher. But if you're just starting out, Docker Compose is a great way to get started.

Here's an example of how you can configure a Nextcloud instance to use a MariaDB database:
```yaml
---
version: '3'
services:
  nextcloud:
    image: nextcloud:latest
    ports:
      - 8080:80
    environment:
      - MYSQL_ROOT_PASSWORD=my_secret_password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=my_user
      - MYSQL_PASSWORD=my_password
    volumes:
      - /path/to/data:/var/www/html
  mariadb:
    image: mariadb:latest
    environment:
      - MYSQL_ROOT_PASSWORD=my_secret_password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=my_user
      - MYSQL_PASSWORD=my_password
    volumes:
      - /path/to/data/mariadb:/var/lib/mysql
```

### The Verdict

So, does anyone still run their homelab on plain Linux + Docker Compose? The answer is yes, but it's not the most efficient way to manage a homelab. If you're just starting out, it's a great way to get started, but if you're dealing with multiple services, you'll want to consider using a more advanced orchestration tool.

**FAQ**

### Q: What about using Podman instead of Docker?

A: I love Podman, but if you're already invested in the Docker ecosystem, it's not worth switching.

### Q: How do I configure my services to use a MariaDB database?

A: You can use a separate Docker Compose file for your MariaDB instance, or you can configure your services to use an existing MariaDB instance.

### Q: What about using Kubernetes or Rancher?

A: If you're dealing with multiple services, you'll want to consider using a more advanced orchestration tool like Kubernetes or Rancher.

**FAQPage JSON-LD schema**
```json
{
  "@context": "https://schema.org",
  "identifier": "https://example.com/homelab",
  "name": "Homelab Tutorial",
  "description": "A step-by-step guide to setting up a homelab using plain Linux + Docker Compose",
  "keywords": ["selfhosted", "vps", "linux", "technology"],
  "faq": [
    {
      "question": "What about using Podman instead of Docker?",
      "answer": "I love Podman, but if you're already invested in the Docker ecosystem, it's not worth switching."
    },
    {
      "question": "How do I configure my services to use a MariaDB database?",
      "answer": "You can use a separate Docker Compose file for your MariaDB instance, or you can configure your services to use an existing MariaDB instance."
    },
    {
      "question": "What about using Kubernetes or Rancher?",
      "answer": "If you're dealing with multiple services, you'll want to consider using a more advanced orchestration tool like Kubernetes or Rancher."
    }
  ]
}