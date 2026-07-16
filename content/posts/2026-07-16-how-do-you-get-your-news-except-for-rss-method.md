---
title: "Beyond RSS: 5 SelfâHosted Ways to Fetch Your Daily News (Proven by r/selfhosted Users)"
date: 2026-07-16T17:49:05+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Discover the communityâtested, selfâhosted alternatives to RSS for pulling newsâemail newsletters, ActivityPub, Telegram bots, and more."
---

## The Community Spark  

The r/selfhosted thread *âHow do you get your NEWS? (Except for RSS method)â* exploded after a user announced they were ditching RSS for privacy reasons. Within hours, dozens of seasoned selfâhosters chimed in, sharing scripts, Docker images, and even homemade newsletters. The core question? **How can you stay informed without relying on thirdâparty RSS aggregators?**  

## Synthesized Community Perspectives  

| Consensus | Debate |
|-----------|--------|
| **Privacy first** â Most users prefer solutions that keep URLs, clickâstreams, and email addresses on their own VPS. | **Complexity vs. Simplicity** â Some argued a single Docker compose file (e.g., Miniflux) is easier than a custom scraper, even if it still uses RSS internally. |
| **Federated timelines** â ActivityPubâbased feeds (Mastodon, Misskey) are celebrated for being open and selfâhostable. | **Maintenance overhead** â Running a fullâstack web scraper (Python + cron) raised concerns about breakage when sites change layouts. |
| **Email as a universal format** â Listmonk, Mailtrain, and selfâhosted newsletters were highlighted for offline reading on any device. | **Push fatigue** â Realâtime push services (Gotify, Pushjet) can overwhelm users if not throttled correctly. |

The communityâs lived experience shows that a hybrid approachâcombining federated feeds, selfâhosted newsletters, and lightweight push botsâcovers most useâcases while preserving sovereignty.

## DeepâDive Actionable Guide  

Below are the five mostâvoted solutions, each with a concise, reproducible setup.

### 1. SelfâHosted Email Newsletters with **Listmonk**  

Listmonk turns a mailing list into a private newsletter. Pair it with a simple *scrapeâandâmail* script.

```bash
# 1ï¸â£ Install Listmonk via Docker
docker run -d --name listmonk \
  -p 9000:9000 \
  -e "LISTMONK_DB_HOST=postgres" \
  -e "LISTMONK_DB_PORT=5432" \
  -e "LISTMONK_DB_USER=listmonk" \
  -e "LISTMONK_DB_PASSWORD=securepwd" \
  -e "LISTMONK_DB_NAME=listmonk" \
  ghcr.io/knadh/listmonk:latest

# 2ï¸â£ Python scraper (newsscrape.py)
pip install newspaper3k
cat > newsscrape.py <<'PY'
import newspaper, smtplib, os
from email.message import EmailMessage

urls = ["https://example.com/tech", "https://example.org/world"]
msg = EmailMessage()
msg["Subject"] = "Your Daily SelfâHosted Digest"
msg["From"] = "digest@yourdomain.com"
msg["To"] = "you@yourdomain.com"

body = ""
for u in urls:
    article = newspaper.Article(u)
    article.download()
    article.parse()
    body += f"ð° {article.title}\n{article.summary}\n{u}\n\n"

msg.set_content(body)
with smtplib.SMTP("localhost") as s:
    s.send_message(msg)
PY

# 3ï¸â£ Cron job (run at 07:00)
0 7 * * * /usr/bin/python3 /home/user/newsscrape.py
```

**Result:** Every morning you receive a plainâtext digest in your inbox, stored on your own mail server (Postfix/Dovecot). No thirdâparty trackers.

### 2. Federated Social Streams via **Mastodon**  

If you already run a Mastodon instance, you can follow news accounts and pull their *ActivityPub* timelines with a single API call.

```bash
# Get your access token (replace with your credentials)
TOKEN=$(curl -s -X POST https://mastodon.yourdomain/api/v1/apps \
  -d "client_name=NewsBot" -d "scopes=read" -d "redirect_uris=urn:ietf:wg:oauth:2.0:oob" \
  | jq -r .client_id)

# Fetch home timeline (JSON)
curl -H "Authorization: Bearer $TOKEN" \
  https://mastodon.yourdomain/api/v1/timelines/home?limit=20 > home.json
```

Parse `home.json` with `jq` or a tiny Go program to render a static HTML page (`/var/www/news/index.html`). The page can be served by Nginx and cached for offline reading.

### 3. Telegram Bot Digest with **Gotify** + **PythonâTelegramâBot**  

Gotify is a lightweight push notification server you host on the same VPS. Combine it with a Telegram bot that forwards selected articles.

```bash
# Gotify via Docker
docker run -d --name gotify -p 8080:80 \
  -e "GO_ENV=production" \
  ghcr.io/gotify/server

# Minimal bot (bot.py)
pip install python-telegram-bot requests
cat > bot.py <<'PY'
import telegram, requests, os, time
BOT = telegram.Bot(token=os.getenv("TG_TOKEN"))
GOTIFY_URL = "http://localhost:8080/message?token=gotify_token"

def send_news():
    # Example: pull headlines from Hacker News API
    resp = requests.get("https://hacker-news.firebaseio.com/v0/topstories.json")
    ids = resp.json()[:5]
    for i in ids:
        item = requests.get(f"https://hacker-news.firebaseio.com/v0/item/{i}.json").json()
        text = f"{item['title']}\n{item['url']}"
        BOT.send_message(chat_id=os.getenv("TG_CHAT"), text=text)
        # Also push to Gotify
        requests.post(GOTIFY_URL, json={"title":"HN", "message":text})

while True:
    send_news()
    time.sleep(86400)  # daily
PY

# Run as systemd service
```

**Result:** You receive a Telegram message *and* a Gotify push on any device (Android, iOS, desktop).

### 4. Static Site Generator + Scrapy (Python)  

For users who love reading in a browser, generate a static site each night.

```bash
# Scrapy project (news_spider.py)
pip install scrapy
cat > news_spider.py <<'PY'
import scrapy

class NewsSpider(scrapy.Spider):
    name = "news"
    start_urls = ["https://example.com/tech", "https://example.org/world"]

    def parse(self, response):
        for article in response.css('article'):
            yield {
                "title": article.css('h2::text').get(),
                "url": article.css('a::attr(href)').get(),
                "summary": article.css('p.summary::text').get(),
            }
PY

# Run and render with Jinja2 template
scrapy runspider news_spider.py -o articles.json
jinja2 template.html articles.json > /var/www/news/index.html
```

Host the directory with Nginx and enable **CacheâControl** headers for offline access.

### 5. PushâOnly Alerts with **Gotify** + Bash  

If you only need headlines, a pure Bash solution may suffice.

```bash
#!/usr/bin/env bash
TOKEN="gotify_token"
URL="https://news.ycombinator.com/rss"

# Extract titles (no RSS parser, just grep)
curl -s "$URL" | grep -oP '(?<=<title>).*?(?=</title>)' | tail -n +2 | head -5 |
while read -r line; do
  curl -s -F "title=HN" -F "message=$line" "http://localhost:8080/message?token=$TOKEN"
done
```

Add to cron (`30 8 * * * /home/user/hn_gotify.sh`) for a quick 8â¯am push.

## Pros & Cons Comparison  

| Method | Privacy | Setup Complexity | RealâTime | Offline Access | Best For |
|--------|---------|------------------|-----------|----------------|----------|
| Listmonk + scraper | |âââ (Docker + Python) | âï¸ | (email) | Readers who love email |
| Mastodon ActivityPub | |ââ (API token) | âï¸ |â (cached HTML) | Socialâmedia enthusiasts |
| Telegram Bot + Gotify |â |âââ (Docker + bot) | |âââ (push only) | Mobileâfirst users |
| Scrapy + static site | |â (Python + templating) | âï¸ | | Browserâcentric readers |
| Pure Bash Gotify | |ââââ (single script) | | âï¸ | Minimalists needing headlines only |

## The Verdict / Expert Advice  

1. **If email is your lifeline** â Deploy Listmonk. Its UI makes list management painless, and the script can be expanded to any source.  
2. **If you already run a Fediverse instance** â Leverage Mastodonâs native ActivityPub feed; you stay in the same privacy sandbox.  
3. **If you crave instant alerts on phone and desktop** â Combine a Telegram bot with Gotify for dual push channels.  
4. **If you prefer a clean, adâfree web archive** â Build a static site with Scrapy; host it on any cheap VPS.  
5. **If you want the simplest âheadlineâonlyâ solution** â The BashâGotify oneâliner is unbeatable.

Mix and match: many community members run Listmonk for daily digests *and* Gotify for urgent breaking news, achieving a balanced workflow.

## Frequently Asked Questions  

**Q1: Can I replace RSS completely with these methods?**  
Yes. All five solutions fetch content directly via HTTP APIs or web scraping, bypassing RSS entirely while keeping data on your own server.

**Q2: Do I need a dedicated VPS for each tool?**  
Not necessarily. Docker Compose lets you spin up Listmonk, Gotify, and even Mastodon on a single VPS (2â4â¯GB RAM recommended). The staticâsite generator runs as a cron job on the same host.

**Q3: How do I keep the scrapers from breaking when sites change layout?**  
Use robust libraries like **newspaper3k** (for articles) or **Scrapy** selectors that target semantic HTML (e.g., `article` tags). Versionâcontrol your spider and add a nightly test that alerts you via Gotify if parsing fails.

**Q4: Is there a way to encrypt the newsletters sent by Listmonk?**  
Configure Postfix/Dovecot with TLS (STARTTLS) and enable S/MIME or PGP signing in your mail client. The content stays on your server, and transport encryption prevents eavesdropping.

---