---
title: Building a Homelab Homepage You’ll Actually Like
date: '2026-09-02 22:00:03+08:00'
draft: false
tags:
- selfhosted
- homepage
- homelab
- linux
summary: How I built a sleek, functional homelab homepage that finally doesn’t make
  my eyeballs hate me.
---

Look, I’ve been through *a lot* of janky homelab homepages. Startpage clones, ancient dashboards with buttons that barely work, or wildly over-engineered Grafana setups that tanked my Raspberry Pi the second I added animations. After one too many Reddit threads roasting carousels (seriously, just don’t—never add a carousel), I finally landed on a setup I’m happy with. Here’s how I built it, step by step.

## The Tools I Chose  

After trying a million approaches, I landed on a combination of [Dashy](https://github.com/Lissy93/dashy) and a backend with Docker Compose. Dashy’s been having a moment on r/selfhosted (it's basically the friendly, feature-packed alternative to Heimdall), and I can confirm it hits all the right notes. Sleek UI? Check. Ridiculous customization options for your OCD brain? Double-check. Easy deployment? Absolutely.

This combo works for me because it balances resource use and configurability. Dashy’s pretty lightweight (50-70MB RAM in my tests) once it’s up and running. If you care about *real* minimalism, Heimdall might run a bit lighter (~30MB), but I wanted more flair and frequently-used service previews, so Dashy it was.

## Step 1: Set Up Docker Compose  

If you’re not using Docker Compose for services like this, you’re doing too much manual labor. Save your sanity. Here’s my working `docker-compose.yml` file for Dashy:

```yaml
version: "3.8"

services:
  dashy:
    image: lissy93/dashy:latest
    container_name: dashy
    ports:
      - "8080:80"
    volumes:
      - ./dashy_userconf.yml:/app/public/conf.yml
    restart: unless-stopped
```

Pop that into a new directory, named something handy like `/srv/dashy/`, and fire it up:

```bash
docker-compose up -d
```

Dashy handles being reverse proxied like a champ, so if you’ve already got Nginx or Traefik running, it’ll slot in smoothly.

## Step 2: Configuring Dashy  

Once your container’s up, Dashy’s default dashboard is… fine. Let’s be real—nobody keeps the defaults. First, I went straight for the custom `conf.yml` setup. You can pull your `conf.yml` file by copying it out of the container first:

```bash
docker cp dashy:/app/public/conf.yml ./dashy_userconf.yml
```

Open that file, and the real tinkering begins. Here’s an example of one of my custom entries:

```yaml
appConfig:
  theme: material-dark
  customTitle: "My Homelab Dashboard"
  navBar: true
  footer: true

sections:
  - name: "Media"
    items:
      - title: "Plex"
        description: "Media Server"
        icon: "mdi-movie"
        url: "http://plex.local"
        target: "_self"
      - title: "Calibre"
        description: "Ebook Library"
        icon: "mdi-book-open"
        url: "http://calibre.local"
  - name: "Dev Tools"
    items:
      - title: "Portainer"
        description: "Container Management"
        icon: "mdi-docker"
        url: "http://portainer.local"
```

Don’t skip the icons! Dashy uses Material Design Icons, so head over to [their site](https://materialdesignicons.com/) to find the perfect one for each service.

## Step 3: Add Reverse Proxy (Optional but Smart)

If you care about TLS (and you should), throw Dashy under a reverse proxy. I use Nginx because it’s what I started with years ago, but Traefik fans, feel free to look away. Here’s my Nginx snippet for Dashy running on `https://dashboard.myhomelab.local`:

```nginx
server {
    listen 443 ssl;
    server_name dashboard.myhomelab.local;

    ssl_certificate /etc/letsencrypt/live/dashboard.myhomelab.local/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/dashboard.myhomelab.local/privkey.pem;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Throw it into your Nginx config and restart:

```bash
sudo systemctl restart nginx
```

Let’s Encrypt for the SSL certs is the obvious play here. There’s zero reason not to use it.

## Why This Worked for Me

What makes this setup stick isn’t just that it works—it’s that I *want* to look at it. I kept the theme slick and dark, stayed away from over-complicating it with widgets I’d forget to check, and made navigation between my services fast. No one wants to spend 30 seconds hunting for their Plex link.

Now, if you’re running this on ARM (e.g., a Raspberry Pi), be careful with what extra features you enable in Dashy. Some users on r/selfhosted have reported performance dips when overloading ARM-powered setups, particularly with heavier icons or animations. For x86 VPS users (like Hetzner or Linode), you’re golden to go wild.

---

## FAQs

### Why use Dashy instead of Heimdall?  
Dashy gives you way more options for customization, like themes, icons, and even service status monitoring. Heimdall is simpler and lighter but looks… plain. If you want minimalism and no frills, stick with Heimdall. But if you want a dashboard that actually feels polished, Dashy all the way.

### Does this work on a Raspberry Pi?  
Yes, but tread lightly. Dashy runs fine on a Raspberry Pi 4 with 4GB RAM, but you might get laggy if you overload it with fancy animations or previews, especially if the Pi’s handling other workloads.

### How do I back up my Dashy config?  
Keep your `conf.yml` version-controlled. I sync mine with a private Git repo on my NAS. If you forget, you can always `docker cp` it back out of the container, but don’t make that a habit. Automated backups are your friend.
