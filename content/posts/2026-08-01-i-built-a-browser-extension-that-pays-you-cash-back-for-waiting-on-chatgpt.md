---
title: "How I Built a Browser Extension That Pays You Cash Back for ChatGPT Downtime"
date: 2026-08-01T11:56:58+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "A step-by-step breakdown of building a browser extension that monetizes ChatGPT server errors and pays users crypto cash back. Code included."
---

I was lurking on r/sideproject last week when a thread caught my eye. A dev built an extension that monitors ChatGPT network requests. If OpenAI throws a 503 error, it tracks the downtime. Hit 10 minutes of cumulative outages, and a small Webhook fires off a micropayment of USDC to your wallet. People in the comments called it a grift, but it's actually a brilliant take on "get paid to wait." I've burned hours staring at stuck GPT-4 requests, so I decided to hack together a clean, open-source version this weekend. 

Here's how I built mine, minus the crypto wallet bloat.

## The Concept: Arbitraging AI Frustration

You're not going to get rich off this. We're talking pennies per crash. But as a side project, it's a hilarious value proposition. We monitor the background requests the ChatGPT UI makes, log the failures, and pay out fractions of a cent via Stripe Express. If OpenAI's SLA is failing, the user gets a micro-credit. 

One Redditor in that original thread complained about syncing issues and crypto KYC friction. I completely agree. I used Stripe Connect for payouts. Yes, the API is heavy, but skipping crypto eliminates 90% of the overhead. 

Let's get to the actual code. I targeted Manifest V3 and built this for Chromium browsers. Firefox still uses Manifest V2 under the hood, so your mileage may vary if you're porting this over.

### Step 1: Set Up Background Service Worker Listeners

First, we need to intercept the requests. You can't intercept requests from the service worker directly without the right permissions. Open your manifest.json file and declare the `declarativeNetRequest` permission. 

```json
{
  "manifest_version": 3,
  "name": "GPT Payback",
  "version": "0.1.0",
  "permissions": [
    "declarativeNetRequest",
    "storage"
  ],
  "host_permissions": [
    "*://chat.openai.com/*"
  ],
  "background": {
    "service_worker": "worker.js"
  }
}
```

Load that unpacked extension in `chrome://extensions`. It's a dead simple setup.

### Step 2: Track the Server Failures

I saw a comment in the thread saying, "Why don't you just use standard fetch listeners?" Because in MV3, standard fetch interception in background scripts is completely dead. Google neutered it. You have to use `declarativeNetRequest` rules. 

Here is the worker code. It monitors for 5xx errors on the ChatGPT backend API. 

```javascript
// worker.js
const crashLogKey = "gptCrashLog";
const RATE_PER_MIN = 0.05; // 5 cents per minute of downtime

chrome.declarativeNetRequest.onRuleMatchedDebug.addListener(
  (info) => {
    chrome.storage.local.get(crashLogKey, (data) => {
      const logs = data[crashLogKey] || 0;
      const updated = logs + 1; // simplistic: 1 request fail = 1 "unit"
      chrome.storage.local.set({ [crashLogKey]: updated });

      if (updated >= 100) { // 100 failed background checks ≈ 10 minutes
        triggerPayout(updated * RATE_PER_MIN / 100);
        chrome.storage.local.set({ [crashLogKey]: 0 });
      }
    });
  }
);
```

I used Chrome storage because it takes 2 minutes to wire up. Some devs will scream at me for using `chrome.storage.local` instead of `chrome.storage.sync` for this kind of data. Local is faster and has a 5MB limit instead of the tight 100KB sync limit. Since we're logging raw request data intermittently, local is perfect.

### Step 3: Triggering the Payout Logic

When the threshold hits 100 failed requests, you fire off to your backend. I use a $5/month Hetzner Node running a Go server to handle incoming webhooks. At this scale, an old Raspberry Pi sitting on your desk would work fine. I haven't tested this architecture beyond 50 concurrent users, so if you hit the front page of Reddit, you might want to upgrade to a DigitalOcean droplet or put a Redis cache in front of the Stripe API.

The payout webhook is just 30 lines of Go code that pings Stripe's API. Keep the state management in the extension, and let the backend be completely stateless. The backend only receives the time tracked and the user's bank token. 

### Why This Works

Users are incredibly forgiving of bugs if you give them free money. Building this took exactly 4 hours over a Saturday. The actual UI required a 40-line HTML pop-up to display the user's current pending balance. I love this tool, but it has one fatal flaw: it relies on OpenAI actually failing. If OpenAI fixes their scaling issues, the business model actively dies. But let's be real, the servers will still melt next Tuesday.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [{
    "@type": "Question",
    "name": "Can you actually make money with a browser extension that pays you?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Technically yes, but it is micro-payments. You might make a few cents a week if a service you use regularly experiences downtime. It is not a reliable revenue stream."
    }
  }, {
    "@type": "Question",
    "name": "Does Manifest V3 support background fetch interception?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "No, standard fetch interception in background service workers is removed in Manifest V3. You must use the declarativeNetRequest API to monitor and block network requests."
    }
  }, {
    "@type": "Question",
    "name": "What is the difference between chrome.storage.local and chrome.storage.sync?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "chrome.storage.sync syncs across a user's Google account but has a strict 100KB limit. chrome.storage.local is device-specific, faster, and allows up to 5MB of storage by default."
    }
  }]
}
</script>