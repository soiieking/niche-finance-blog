---
title: How to Track NAS Drive Prices Like a Pro (and Spot Fake Discounts)
date: '2026-09-10 08:00:04+08:00'
draft: false
tags:
- selfhosted
- nas
- deals
- linux
summary: Don’t fall for fake NAS drive sales. Learn how to track daily price history
  and find real discounts on storage.
---

Tracking NAS drive prices is a hobby, and maybe a bit of a sickness. If you spend enough time on r/selfhosted, you know the drill: someone posts a deal, and half the comments are, “That’s not a deal, it was $30 cheaper last week.” The other half are, “Why didn’t you buy it a month ago? Prices are insane now.”

Storage is stupidly volatile. Tracking daily prices has saved me from overpaying too many times to count, and it’s easier than you think to automate it. The best part? You’ll stop wasting time with fake “sales” that are really just price resets after a markup.

## Why You Should Track Prices

Sometimes a "sale" is just marketing. A Seagate Exos X16 16TB at $219 looks good—until you realize it was $179 four weeks ago. Sure, storage trends up over time, but price dips do happen, and you have to be ready to pounce. No spreadsheet-in-your-head nonsense. Automate price tracking and know the *actual* history.

Someone in the NAS build thread mentioned using camelcamelcamel for this. It’s decent for Amazon, but I wanted broader coverage: Newegg, BHPhoto, even local shops if they expose price data. The solution? Scrape and log prices daily using Python and a cheap VPS.

## What You’ll Need

1. A VPS (or a spare machine lying around). I used a cheap Hetzner CX11 (€4.49/mo) with Ubuntu 22.04. Overkill? Maybe, but it’s reliable.
2. Python 3.9+ (I used 3.10, works fine with older library versions too).
3. Scrapy or requests/BeautifulSoup for scraping. Pick your poison.
4. A basic SQLite database or even a plain CSV during testing.

## Step 1: Set Up Your Environment

Here’s how to slap this together:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install python3 python3-pip git -y
pip3 install requests beautifulsoup4 pandas
```

For logging data, I use pandas because life’s too short to reinvent data wrangling.

## Step 2: Write the Scraper

Basic code to track daily prices. Let’s target Newegg for now. (Modify headers if they block you too aggressively—spoofing a user agent usually fixes it.)

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import datetime

# Example: Scraping a specific Newegg product
def fetch_price(url):
    headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.121 Safari/537.36"}
    response = requests.get(url, headers=headers)
    soup = BeautifulSoup(response.content, 'html.parser')
    
    # Newegg price extraction
    price = soup.select_one('.price .price-current')
    if price:
        return float(price.text.replace('$', '').replace(',', ''))
    return None

def log_price(product_name, url):
    price = fetch_price(url)
    if price:
        data = {'product': product_name, 'price': price, 'timestamp': datetime.datetime.now()}
        return pd.DataFrame([data])
    return None

url = "https://www.newegg.com/p/N82E16822184725"  # Example drive
df = log_price('Seagate Exos X16 16TB', url)

# Save your results to a CSV for now
df.to_csv('price_history.csv', mode='a', header=False, index=False)
```

This runs manually for now, but you’ll automate it with cron soon.

## Step 3: Automate with Cron

Once the script works, schedule it with cron. The default storage on my VPS is tiny, so I cleaned CSVs weekly and archived to a cheap Backblaze B2 bucket.

```bash
crontab -e
```

Add this line to fetch prices every night:

```bash
0 3 * * * python3 /path/to/your_script.py
```

If you’re feeling fancy, switch to SQLite (pandas supports it) or push the data to a self-hosted Grafana dashboard via InfluxDB. Your call.

## Step 4: Visualize History (Optional)

Use pandas to graph the data locally:

```python
import matplotlib.pyplot as plt
df = pd.read_csv('price_history.csv', names=['product', 'price', 'timestamp'])
df['timestamp'] = pd.to_datetime(df['timestamp'])
df.set_index('timestamp', inplace=True)

# Plot price history
df['price'].plot()
plt.title('Price History')
plt.xlabel('Date')
plt.ylabel('Price (USD)')
plt.show()
```

This might feel overkill, but when you spot that $179-to-$219 fake sale, your smugness is justified.

## Limitations and Nuances

1. **Scraper Breakage**: If Newegg (or any site) changes their HTML, the scraper dies. You have to fix selectors. Alternatives? Web APIs for supported stores, or services like Keepa if you don’t mind Amazon lock-in.
2. **Blocked Requests**: Some sites hate scrapers. Rotate user agents, or use proxy services like ScraperAPI ($29/mo) if you hit a wall.
3. **Regional Pricing**: Deals vary by region. Tracking local shops? Doable if you’re patient with manual scraping setup.

## FAQ

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why not use camelcamelcamel or Keepa?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Camelcamelcamel is Amazon-only, and Keepa has a paywall for advanced features. Scraping gives you control over other retailers like Newegg, BHPhoto, or local shops."
      }
    },
    {
      "@type": "Question",
      "name": "Can I run this on ARM?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, if Python and dependencies like pandas work. Tested on Raspberry Pi 4 (Raspbian), but performance with large datasets might suffer."
      }
    },
    {
      "@type": "Question",
      "name": "What’s the cheapest VPS for this?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Hetzner CX11 (€4.49/mo) or Oracle Cloud's always-free tier. Just make sure it can run cron jobs uninterrupted."
      }
    }
  ]
}
</script>

---

Got questions? Drop a comment on r/selfhosted or DM me. You’ll never overpay for a NAS drive again.
