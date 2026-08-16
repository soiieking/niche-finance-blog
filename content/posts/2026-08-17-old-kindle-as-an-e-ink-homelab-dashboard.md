---
title: "Reviving Old Kindles as E-Ink Homelab Dashboards"
date: 2026-08-17T06:00:13+08:00
draft: false
tags: ["selfhosted", "homelab", "eink", "kindle"]
summary: "Breathe new life into old Kindles as homelab dashboards"
---

I've spent countless hours in r/selfhosted, and one topic that caught my eye was repurposing old Kindles as e-ink homelab dashboards. The idea sounds gimmicky, but I had to try it. I mean, who doesn't have an old Kindle collecting dust somewhere? u/TechNoLogic's comment about using a Kindle 3 with a custom dashboard really got me going – I had a similar model lying around.

## The Why
The main advantage here is the e-ink display's ridiculously low power consumption – we're talking weeks on a single charge. Perfect for a homelab dashboard that's always on. I set up my Kindle 3 with a Python script to fetch system metrics from my Docker containers, and it worked beautifully. The refresh rate is terrible, but for a static dashboard, it's more than enough.

This is overkill for most people, but I love the idea of reusing old hardware. Plus, it's a great conversation starter. The Kindle's 6-inch display is a bit cramped, but I managed to fit all the essential metrics: CPU usage, memory, disk space, and some custom alerts. I used a combination of Docker and Podman to keep things organized – yes, I'm one of those people who use both.

## The How
Setup was relatively straightforward, but I did encounter some issues with the Kindle's ancient WiFi. I had to use an old router with WPA2 support just to get it connected. If you're planning to try this, make sure your router supports older WiFi protocols. I also had to compile a custom kernel for my Kindle to get the display working properly – not for the faint of heart.

I've been running this setup for a few months now, and it's been rock-solid. The Kindle's battery life is still impressive, even with the constant WiFi connection. I've seen some people use similar setups with newer Kindles, but I think the older models are perfect for this – they're cheap, and you can find them lying around.

### Alternatives
If you don't have an old Kindle lying around, you can consider other e-ink devices like the reMarkable or Onyx Boox. They're more expensive, but offer better displays and more features. I haven't tested this on ARM, but I've heard it's doable with some tweaking. The community is genuinely split on this – some people swear by the reMarkable's superior display, while others prefer the Onyx Boox's Android support.

I ended up spending around $20 on a used Kindle 3 and some cables. Not bad for a unique homelab dashboard. Your mileage may vary, but I think this is a fun project for anyone looking to breathe new life into old hardware.

## FAQ
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What Kindle models are suitable for this project?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Any Kindle model with WiFi support should work, but older models like the Kindle 3 are ideal due to their low cost and availability."
      }
    },
    {
      "@type": "Question",
      "name": "Can I use other e-ink devices for this project?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, devices like the reMarkable or Onyx Boox can be used, but they may require more setup and tweaking."
      }
    },
    {
      "@type": "Question",
      "name": "How long does the setup process take?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Setup time can vary depending on your experience with Linux and Python, but expect to spend around 2-5 hours getting everything working."
      }
    }
  ]
}