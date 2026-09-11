---
title: 'Mining NSFW Subreddits from 12,413 Reddit Comments: Lessons from a Side Project'
date: '2026-09-11 22:00:05+08:00'
draft: false
tags:
- indie-hacker
- data-mining
- reddit
summary: How I scraped and analyzed thousands of Reddit comments to find trending
  NSFW subreddits — tools, lessons, and why it might be overkill.
---

## What I Did (And Why)

Someone dropped a discussion on `r/sideproject` about scraping Reddit comments to discover NSFW subreddits—an impressive mix of "wild idea" and "maybe too much time on their hands." The thread hit a nerve for me. I’ve built dumb things like this before, some successful, some useless, so I figured, why not?

The goal: mine 12,413 comments (and counting) from public subreddits to extract recurring mentions of NSFW communities. Why NSFW? Because, let's be honest, these kinds of subreddits drive discovery on Reddit and *no one talks about it*. Plus, everyone secretly wants to know where the traffic is.

This post lays out how you'd build something similar without blowing a weekend. It’s part tutorial, part honest reality check. Spoiler: the value might not justify the effort, but it sure is fun.

---

## Tools You’ll Need

This setup assumes you're comfortable with Python. If not, don’t worry, Reddit’s API is surprisingly forgiving. 

1. **Python (v3.10 or later)** - Essential. Bonus points if you use a virtualenv.
2. **PRAW** - The Python Reddit API Wrapper. Sounds official, works like butter.
3. **SQLite** or **Postgres** - SQLite is fine for under 10k records. Beyond that, just use Postgres.
4. **Regex and Pandas** - Scraping’s best friends.
5. **Hosting (Optional)** - Hetzner or Linode if you want to share results. I’d avoid AWS for this—it’s literal overkill.

For the record, I tested this workflow on my MacOS 14.0 ARM setup. Your mileage may vary with Windows.

---

## Step-by-Step Scraping

### 1. Set Up Authentication

You’ll need Reddit API keys from [Reddit Apps](https://www.reddit.com/prefs/apps). Don’t overthink it—just set up a personal script app. Example `.env` file:

```env
REDDIT_CLIENT_ID=your-client-id
REDDIT_SECRET=your-secret
REDDIT_USER_AGENT=your-project-name:v1.0 (by u/yourusername)
```

Install PRAW:

```bash
pip install praw
```

### 2. Fetch Comments

Here’s a simple Python script to pull comments:

```python
import praw
import os
from dotenv import load_dotenv

load_dotenv()

reddit = praw.Reddit(
    client_id=os.getenv('REDDIT_CLIENT_ID'),
    client_secret=os.getenv('REDDIT_SECRET'),
    user_agent=os.getenv('REDDIT_USER_AGENT')
)

comments = []
subreddits = ["AskReddit", "NSFW", "GoneWild"]  # Replace with your list.

for subreddit in subreddits:
    for comment in reddit.subreddit(subreddit).comments(limit=500):
        comments.append(comment.body)
```

**Pro-tip:** If you need more comments, use `.submissions()` to grab submission comments by date range. But know this: scraping large datasets without Reddit's blessing could land you on their bad side.

### 3. Clean and Extract NSFW Mentions

Regex is your go-to here:

```python
import re
from collections import Counter

pattern = r"r/([a-zA-Z0-9_]+)"  # Match `r/subreddit_name`.
matches = [re.findall(pattern, comment) for comment in comments]
flat_matches = [item for sublist in matches for item in sublist]

# Count occurrences
counts = Counter(flat_matches)
print(counts.most_common(20))
```

At this stage, you should have a list of NSFW subreddit mentions sorted by frequency. Subreddits like `r/RealGirls` or `r/NSFW_GIF` will bubble up quickly—no surprises, just human nature.

---

## Risk vs Reward

Here’s my hot take: this is overkill **for most projects**. Yes, you’ll find some traffic-driving gems, but without a strong reason (e.g., you're building a curated NSFW site), this is just curiosity in action. The dataset won’t age well either—subreddits peak and die off constantly. 

If I were starting over, I’d limit the scrape to **30-day snapshots** and skip the edge cases (spammy bots or hyper-niche subs).

Cost-wise: expect 2-5 hours of upfront dev work, $0 with free-tier hosting, and peanuts for SQLite-level data storage.

---

## Lessons from r/SideProject

A couple of gems from the original thread:
- One user mentioned Google Trends as a shortcut to track NSFW term searches over time—a brilliant way to validate your findings. 
- Another warned about Reddit’s rate limits. Always spread scraping jobs over hours, not minutes, unless you want to deal with 429s.
- Finally, someone asked why they need to reinvent the wheel when niche NSFW aggregators already exist. Fair point, but where’s the fun in that?

---

## FAQ

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Is this project against Reddit's rules?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Technically, scraping public data is fine, but Reddit's API terms warn against excessive scraping. Use their API responsibly and don't automate without limits."
      }
    },
    {
      "@type": "Question",
      "name": "Why NSFW subreddits and not another niche?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "NSFW subreddits are high traffic and constantly changing. The project is really about understanding trends, and NSFW content makes a great case study for that."
      }
    },
    {
      "@type": "Question",
      "name": "Can I scale this for large datasets?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, but you'll need better tools—Redash or Superset for visualization, Postgres for storage, and a small VPS for hosting. Don’t expect SQLite to hold up past 100k records."
      }
    }
  ]
}
</script>
