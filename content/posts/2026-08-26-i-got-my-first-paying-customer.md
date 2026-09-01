---
title: My First Paying Customer (and How You Can Get One Too)
date: '2026-08-26T10:00:27+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: Lessons learned from my first paying customer and how to replicate their
  success
---

I just got my first paying customer for my side project, and I'm still reeling from the excitement. It's a big deal, folks. This is what it's all about. I've spent countless hours building, testing, and iterating on my project, and now someone is willing to pay for it.
**Step 1: Validate Your Idea**
Before you start building, make sure you have a solid idea. I spent months researching and validating my idea on r/sideproject, and it paid off. I asked for feedback, tested different approaches, and even created a landing page to gauge interest. One of the most valuable pieces of feedback I received was from u/throwaway12345678: "Make sure you have a clear value proposition and a well-defined target audience."
For my project, I used a landing page hosted on GitHub Pages with a simple HTML form to collect email addresses from interested users. I also created a Twitter thread to gauge interest and get feedback on my idea. Here's an example of my landing page: <https://example.com/landing-page>
**Step 2: Build a Minimum Viable Product**
Once you have a solid idea, it's time to build a minimum viable product (MVP). This will allow you to test your idea with real users and gather feedback. I built my MVP using a combination of Docker and Hugo. I love Docker, but I've heard some people swear by Podman (e.g., u/podmanfanboy: "Podman is way faster than Docker"). For my project, I used Docker version 20.10.7 and Hugo version 0.96.2.
Here's an example of my Dockerfile:
```dockerfile
FROM hugo:0.96.2
WORKDIR /app
COPY . /app
RUN hugo --buildDrafts --buildFuture
EXPOSE 1313
CMD ["hugo", "server", "-D"]
```
**Step 3: Get Your First Customer**
Now it's time to get your first customer. This is the hardest part, but it's also the most rewarding. I used a combination of social media and online communities to promote my project and attract customers. I created a Twitter thread to announce my project and used relevant hashtags to reach a wider audience.
One of the most valuable pieces of advice I received was from u/marketingpro: "Focus on providing value to your customers, and the money will follow." I made sure to provide excellent customer support and continuously improve my product based on customer feedback.
**FAQ**
Q: How do I validate my idea?
A: Use online communities like r/sideproject to get feedback and test your idea with real users.
Q: What's the best way to build a minimum viable product?
A: Use a combination of Docker and Hugo to quickly build and deploy a minimum viable product.
Q: How do I get my first customer?
A: Use social media and online communities to promote your project and attract customers. Focus on providing value to your customers, and the money will follow.
**JSON-LD FAQ Schema**
```json
{
  "@context": "https://schema.org/",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "How do I validate my idea?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Use online communities like r/sideproject to get feedback and test your idea with real users."
      }
    },
    {
      "@type": "Question",
      "name": "What's the best way to build a minimum viable product?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Use a combination of Docker and Hugo to quickly build and deploy a minimum viable product."
      }
    },
    {
      "@type": "Question",
      "name": "How do I get my first customer?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Use social media and online communities to promote your project and attract customers. Focus on providing value to your customers, and the money will follow."
      }
    }
  ]
}
