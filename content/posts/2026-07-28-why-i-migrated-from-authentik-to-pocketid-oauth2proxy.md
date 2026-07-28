---
title: "Why I Migrated from Authentik to PocketID + OAuth2Proxy: A Self-Hoster's Guide"
date: 2026-07-28T14:19:18+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Discover why the r/selfhosted community is ditching Authentik for the lightweight PocketID + OAuth2 Proxy stack. Learn how to migrate your reverse proxy securely."
---

## The Community Spark

Recently, a massive wave of discussions has swept through the `r/selfhosted` community regarding identity and access management (IAM). While Authentik has long been the gold standard for self-hosted single sign-on (SSO), users are increasingly frustrated by its bloat, heavy resource consumption, and complex update cycles. The trending consensus? A mass migration to the lightweight **PocketID + OAuth2 Proxy** stack. 

But why are homelabbers and professional sysadmins alike abandoning a feature-rich powerhouse for a minimalist approach? Let's break down the lived experiences of the community and explore how to execute this migration seamlessly.

## Synthesized Community Perspectives

The community debate around Authentik versus PocketID centers on one core philosophy: **complexity versus simplicity.**

*   **The Authentik Argument:** Authentik offers enterprise-grade features—LDAP providers, SAML, advanced conditional access, and intricate routing flows. However, users report that running a Redis cache, a PostgreSQL database, and a worker container on modest VPS hardware is overkill for a family or small team setup. Updates frequently break custom flows, causing frustrating downtime.
*   **The PocketID + OAuth2 Proxy Consensus:** The community agrees that 90% of homelabbers only need OpenID Connect (OIDC) for browser-based authentication. PocketID delivers exactly this using a lightweight SQLite database, requiring a fraction of the RAM. When paired with OAuth2 Proxy in front of unsupported apps, it creates an unbreakable, low-maintenance authentication barrier. The consensus is clear: if you aren't managing an enterprise fleet, PocketID is the superior, stress-free choice.

## Deep-Dive Actionable Guide: Migrating Your Stack

Migrating from Authentik to PocketID + OAuth2 Proxy requires ensuring OAuth2 Proxy supports the newer OIDC discovery endpoints. Here is a battle-tested Docker Compose configuration to get your stack running.

### 1. Deploy PocketID via Docker Compose

Start by spinning up PocketID as your central identity provider (IdP).

```yaml
# docker-compose.yml
version: '3.8'
services:
  pocketid:
    image: stonith404/pocketid:latest
    container_name: pocketid
    environment:
      - PUBLIC_URL=https://id.yourdomain.com
      - TRUST_PROXY=true
    volumes:
      - ./pocketid-data:/data
    ports:
      - "8080:80"
    restart: unless-stopped
```

### 2. Configure OAuth2 Proxy for Legacy Apps

Many internal apps lack native OIDC support. OAuth2 Proxy sits in front of them, validating the user against PocketID before allowing traffic. Ensure your `config.cfg` points to PocketID's OIDC endpoints.

```ini
# oauth2-proxy.cfg
# Point to your new PocketID instance
oidc_issuer_url = "https://id.yourdomain.com"
client_id = "your-pocketid-client-id"
client_secret = "your-pocketid-client-secret"

# Email domains allowed to authenticate
email_domains = ["yourdomain.com"]

# Upstream service you are protecting
upstreams = ["http://unprotected-app:3000"]

# Cookie settings for security
cookie_secure = "true"
cookie_samesite = "lax"
```

### 3. Reverse Proxy Integration

In your reverse proxy (e.g., Caddy or Nginx Proxy Manager), route traffic for your protected app to OAuth2 Proxy instead of the app itself. OAuth2 Proxy will handle the redirect to PocketID for login.

## Pros & Cons: Comparative Analysis

Before committing, evaluate which solution aligns with your infrastructure needs.

| Feature | Authentik | PocketID + OAuth2 Proxy |
| :--- | :--- | :--- |
| **RAM Usage (Idle)** | 500MB - 1GB+ | ~50MB - 80MB |
| **Database** | PostgreSQL + Redis | SQLite |
| **Protocol Support** | SAML, LDAP, OIDC, Radius | OIDC only |
| **Setup Time** | 1-2 Hours (Complex) | 10 Minutes (Simple) |
| **App Integration** | Native OIDC / Proxy | Native OIDC / External Proxy |
| **Maintenance** | Frequent breaking updates | Set-and-forget |
| **Best For** | Enterprise / Complex Orgs | Homelab / Small Teams |

## The Verdict: Expert Advice

If you are operating a large homelab, a small business requiring SAML/LDAP, or you rely heavily on complex conditional access rules, **stay with Authentik**. The resource overhead is the price of enterprise-grade flexibility.

However, if you are a self-hoster who just wants to protect your dashboard, media servers, and basic web apps from the open internet without melting your VPS, **migrate to PocketID + OAuth2 Proxy**. The reduction in CPU, RAM, and cognitive overhead (during updates) makes it the definitive 2026 standard for lightweight self-hosted SSO.

## Frequently Asked Questions (FAQ)

**Is PocketID secure enough for production environments?**
Yes, PocketID uses standard, heavily vetted OIDC flows. Its security model relies on simplicity, meaning a smaller attack surface compared to multifaceted solutions like Authentik.

**Can I still protect apps that don't support OIDC natively?**
Absolutely. You place OAuth2 Proxy in front of the application. The proxy intercepts requests, checks authentication against PocketID, and only forwards traffic to the upstream app if the user is logged in.

**Does PocketID support multi-factor authentication (MFA)?**
Yes, PocketID supports WebAuthn (passkeys) natively out of the box, allowing you to enforce 2FA without dealing with legacy OTP systems.

**Will I lose my existing users when migrating from Authentik?**
Yes, because PocketID uses SQLite and Authentik uses PostgreSQL, you must manually recreate your users in PocketID. For homelabbers with under 10 users, this manual migration takes less than five minutes.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Is PocketID secure enough for production environments?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, PocketID uses standard, heavily vetted OIDC flows. Its security model relies on simplicity, meaning a smaller attack surface compared to multifaceted solutions like Authentik."
      }
    },
    {
      "@type": "Question",
      "name": "Can I still protect apps that don't support OIDC natively?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Absolutely. You place OAuth2 Proxy in front of the application. The proxy intercepts requests, checks authentication against PocketID, and only forwards traffic to the upstream app if the user is logged in."
      }
    },
    {
      "@type": "Question",
      "name": "Does PocketID support multi-factor authentication (MFA)?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, PocketID supports WebAuthn (passkeys) natively out of the box, allowing you to enforce 2FA without dealing with legacy OTP systems."
      }
    },
    {
      "@type": "Question",
      "name": "Will I lose my existing users when migrating from Authentik?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, because PocketID uses SQLite and Authentik uses PostgreSQL, you must manually recreate your users in PocketID. For homelabbers with under 10 users, this manual migration takes less than five minutes."
      }
    }
  ]
}
</script>