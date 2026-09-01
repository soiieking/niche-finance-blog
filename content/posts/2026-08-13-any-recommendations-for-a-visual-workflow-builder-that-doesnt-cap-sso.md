---
title: 'Self-Hosted Workflow Builders Without SSO Paywalls: The 2026 Reality Check'
date: '2026-08-13T18:00:40+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Self-Hosted Workflow Builders Without SSO Paywalls: The 2026
  Reality Check.'
---

The r/selfhosted thread asking for a "visual workflow builder that doesn't cap SSO" hit a nerve. And honestly? It should. We're in 2026, and some vendors still treat SAML like it's a luxury feature. That's not a technical limitation — it's a pricing strategy.
Here's the thing: if you're running a homelab or a small business on a Hetzner CX22 (that's €3.79/mo, by the way), paying $50/user/month just to unlock SSO is insulting. The community knows it. The thread had 87 comments in under 24 hours, and the top-voted reply wasn't a tool recommendation — it was someone saying "just use Authelia in front of everything and call it a day."
That's the workaround. But it's not a solution.
## The Three Contenders
### n8n — The Default Answer, With a Catch
n8n is the first name everyone drops. Fair enough — it's mature, has 400+ integrations, and the fair-code license means you can self-host the full version for free. The community edition includes SSO via OAuth2, but SAML? That's locked behind the enterprise tier at $600/month.
Here's the kicker: most people asking for SSO don't actually need SAML. They need "one login for my team." OAuth2 with Authentik or Keycloak does that. I've run n8n behind Authentik for two years — setup took about 20 minutes, and I've never once wished I had SAML.
But if your org mandates SAML for compliance reasons, n8n is out. Don't fight it.
### Activepieces — The Underdog That's Actually Good
Activepieces is the one people sleep on. The self-hosted version is MIT-licensed, which means no feature gating. Period. SSO, SAML, OAuth2, whatever — it's all there because the code is open.
The trade-off? It's younger. Version 0.3.x had some rough edges, but the 0.4+ releases stabilized significantly. RAM usage sits around 300-400MB with Redis and Postgres, which is lighter than n8n's typical 1GB+ footprint.
One real complaint from the thread: the template library is thinner. If you need a pre-built integration for an obscure CRM, you might be building it yourself. But for core stuff — webhooks, email, databases, HTTP requests — it's solid.
### Windmill — The Developer's Choice
Windmill isn't really a visual workflow builder. It's a developer platform with a visual layer. You write scripts in Python or TypeScript, then wire them together visually. It's powerful, but it has a learning curve.
The self-hosted community edition includes SSO (OIDC) without paywalls. The enterprise tier adds SAML, but the community edition is genuinely usable for small teams.
The catch? It's overkill for most people. If you're automating "send me an email when this form is submitted," Windmill is like using a forklift to move a shoebox. But if you're building data pipelines or internal tools, it's the best option here.
## The Authelia/Reverse Proxy Argument
The thread's top comment about putting Authelia in front of everything isn't wrong. It works. I've done it. But here's the problem: it's a band-aid.
When you put Authelia in front of n8n, you get authentication. You don't get user-level permissions inside the app. Everyone who logs in is the same user. That's fine for a homelab with two people. It's not fine when you need to restrict who can edit workflows versus who can only execute them.
If you're solo or a two-person setup, save yourself the headache. Use Authelia or even just a simple Caddy basic auth. If you're a team of five or more, bite the bullet and pick a tool with native SSO.
## What I'd Actually Do
For most people reading this: **Activepieces**. It's free, it's open, and it doesn't treat SSO as a hostage.
For developers who need more power: **Windmill**. The learning curve is real, but the ceiling is higher.
For people who need SAML specifically and can't use Activepieces for some reason: **n8n with OAuth2**, and push back on the SAML requirement. It's usually a checkbox in a compliance doc, not a real need.
## The Bottom Line
The "SSO paywall" problem is manufactured. It exists because vendors know that once you're locked into their workflow builder, switching costs are high. The open-source options have caught up, and the only reason to pay for enterprise tiers is support, not features.
Don't pay for SSO. Self-host something that respects you.
## FAQ
**Can I use n8n with SAML for free?**
No. SAML is enterprise-only in n8n. The community edition supports OAuth2, which works with Authentik, Keycloak, or Authelia.
**Is Activepieces production-ready?**
For small to medium workloads, yes. The 0.4+ releases are stable. For high-volume enterprise use, test thoroughly first — it's younger than n8n.
**What's the cheapest way to self-host a workflow builder?**
A Hetzner CX22 (€3.79/mo) runs Activepieces comfortably with Docker. Add a domain and Caddy for HTTPS, and you're live in under an hour.
<script type="application/ld+json">
{
 "@context": "https://schema.org",
 "@type": "FAQPage",
 "mainEntity": [{
    "@type": "Question",
    "name": "Can I use n8n with SAML for free?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "No. SAML is enterprise-only in n8n. The community edition supports OAuth2, which works with Authentik, Keycloak, or Authelia."
    }
 },{
    "@type": "Question",
    "name": "Is Activepieces production-ready?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "For small to medium workloads, yes. The 0.4+ releases are stable. For high-volume enterprise use, test thoroughly first — it's younger than n8n."
    }
 },{
    "@type": "Question",
    "name": "What's the cheapest way to self-host a workflow builder?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "A Hetzner CX22 (€3.79/mo) runs Activepieces comfortably with Docker. Add a domain and Caddy for HTTPS, and you're live in under an hour."
    }
 }]
}
</script>
