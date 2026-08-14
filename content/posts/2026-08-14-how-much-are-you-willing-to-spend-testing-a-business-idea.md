---
title: "How Much Should You Spend Testing a Business Idea? Real Budgets from r/sideproject"
date: 2026-08-14T08:00:43+08:00
draft: false
tags: ["indie-hacker", "business", "technology"]
summary: "Real budgets from r/sideproject founders. From $0 static sites to $200/mo overkill stacks. Here's what actually works."
---

The question pops up on r/sideproject at least twice a week: *"How much should I spend validating this idea?"* The answers range from "absolutely nothing" to "$500 on ads before writing a line of code." Both are right, and both are wrong.

Here's the thing nobody says out loud: your budget should scale with how painful the problem is. A tool that saves accountants 10 hours a week justifies a $200 test. Another habit tracker app? You should be spending $0 and your Sunday afternoon.

## The $0 Tier: Static Sites and Fake Doors

The cheapest valid test is a landing page with a "Join Waitlist" button. No backend. No database. Just HTML, CSS, and a form that dumps emails into a spreadsheet.

I use Hugo for this because it's one binary, no Node_modules hell, and deploys to Netlify in under a minute. Here's the entire setup:

```bash
hugo new site quicktest
cd quicktest
git init
```

Drop this in `config.yaml`:

```yaml
baseURL: "https://yourdomain.com/"
languageCode: "en-us"
title: "Your Idea Name"
```

Then create `content/_index.md`:

```markdown
---
title: "Stop Wasting Time on Manual Data Entry"
---

# Get early access

We're building [tool name] to automate your spreadsheet nightmares.

<form name="waitlist" netlify>
 <input type="email" name="email" placeholder="you@work.com" required />
 <button type="submit">Join Waitlist</button>
</form>
```

Deploy to Netlify, spend $15 on a domain, and run $50 in Google Ads targeting your exact pain point. Total cost: $65. Time: one evening.

One commenter on the thread nailed it: *"If you can't get 10 people to click 'I want this' for $50, the problem isn't your marketing. It's your idea."* Harsh, but true.

## The $50–$150 Tier: When You Need a Real Product

Some ideas can't be validated with a landing page. If you're building an API, a browser extension, or anything that requires actual usage, you need something functional.

This is where I see people blow it. They spin up a Kubernetes cluster on AWS for a CRUD app. Stop. Here's what actually works:

- **Hetzner CX22** ($4.50/mo) instead of DigitalOcean ($6/mo). Same performance, less money. I've run production workloads on Hetzner for two years without a hiccup.
- **SQLite** instead of PostgreSQL. You don't need a database server until you have concurrent writes. SQLite handles 99% of early-stage traffic.
- **Docker Compose** instead of Kubernetes. If you need K8s before your first paying customer, you're building infrastructure, not a business.

A real example from the thread: a guy built a Slack bot that summarizes long threads. His stack was a $5 VPS, SQLite, and the free Slack API tier. He got 40 signups in two weeks and charged $9/mo. Total spend before first revenue: $10.

## The $200+ Tier: You're Probably Overthinking It

I see people drop $500 on user interviews, $300 on landing page tools, and $200/mo on analytics suites before they've talked to a single customer. This is overkill for most people.

The exception? B2B tools where the buyer is a company, not an individual. If you're selling to procurement departments, you need a polished demo, maybe a SOC 2 report, and a real domain with professional email. That costs money. But even then, you can fake it with a Notion page and a Calendly link.

## The Real Metric: Time, Not Money

Here's what the thread actually taught me. The founders who succeeded didn't optimize for spending less. They optimized for learning faster.

Set a hard deadline. Two weeks. If you haven't talked to 20 potential users or gotten 50 waitlist signups, kill it. The money you save isn't the point — the time you save is.

One founder put it perfectly: *"I spent $0 and 3 months building something nobody wanted. My next idea cost me $80 and 2 weeks to discover the same thing. The $80 was the best investment I ever made."*

## FAQ

### Should I buy a domain before validating?

Yes, but only if it's under $15. A custom domain makes your landing page look legit and costs less than a coffee. Skip the .io domains — they're $40+ and nobody cares.

### Is it worth paying for user interviews?

Only if you're selling B2B. For consumer products, watch people use your free prototype. Their behavior tells you more than their words ever will.

### When should I upgrade from SQLite to PostgreSQL?

When you hit concurrent writes or need features like full-text search. For most side projects, that's after your first 100 paying users. Don't optimize for a problem you don't have yet.

<script type="application/ld+json">
{
 "@context": "https://schema.org",
 "@type": "FAQPage",
 "mainEntity": [{
    "@type": "Question",
    "name": "Should I buy a domain before validating?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Yes, but only if it's under $15. A custom domain makes your landing page look legit and costs less than a coffee. Skip the .io domains — they're $40+ and nobody cares."
    }
 },{
    "@type": "Question",
    "name": "Is it worth paying for user interviews?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Only if you're selling B2B. For consumer products, watch people use your free prototype. Their behavior tells you more than their words ever will."
    }
 },{
    "@type": "Question",
    "name": "When should I upgrade from SQLite to PostgreSQL?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "When you hit concurrent writes or need features like full-text search. For most side projects, that's after your first 100 paying users. Don't optimize for a problem you don't have yet."
    }
 }]
}
</script>