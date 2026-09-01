---
title: List of Self-Hosted Apps That Support SSO/OIDC for Free
date: '2026-09-01 20:00:42+08:00'
draft: false
tags:
- selfhosted
- oidc
- sso
- linux
- technology
summary: SSO on a budget? Here’s a curated list of self-hosted software that plays
  nicely with OIDC for free.
---

Single Sign-On (SSO) is great when it works. Nobody wants to remember 47 different passwords or juggle resets every month. But integrating SSO into your self-hosted setup can feel like pulling teeth—unless you know where to look. After wading through r/selfhosted (and smashing my face against test configs), here’s a short-but-sweet roundup of apps that support SSO or OIDC *and won’t charge you a dime for it*.

Let’s get into it.

## 1. Authentik
**Why it’s great**: If you’re self-hosting, you’ve probably heard of [Authentik](https://goauthentik.io/). It’s the Swiss Army knife of identity providers, offering SSO, OIDC, LDAP, and more. Plus, it’s free. 

I’ve been running it for over a year on an 8GB RAM VPS from Hetzner, and it’s rock solid. The learning curve? Steep. Getting the dependencies set up in Docker or Kubernetes takes some patience. But once it’s humming, you can hook it up to almost anything—Nextcloud, Gitea, Vaultwarden, you name it. People on r/selfhosted rave about this one, and for good reason: unlimited users, no paywalls.

**Watch out for**: Authentik can be overkill for simpler setups. If you just want users to log in to Jellyfin without setting up a central IdP (Identity Provider), this might not be the right call.

---

## 2. Keycloak
**Why it’s great**: Another heavy hitter in this space is [Keycloak](https://www.keycloak.org/). It’s an enterprise-grade identity solution from Red Hat, but 100% open source. Same deal as Authentik: SSO, OIDC, and LDAP baked right in.

I set this up once for a friend’s entirely-too-complicated homelab. (Keycloak and Kubernetes together? Pain.) Once running, it’s flawless. Tons of people use it in production because it supports everything from SAML to multi-tenancy.

**The catch**: Configuration is a nightmare if you’re not already familiar with Java-based apps. Seriously. Memory requirements are also no joke; you’ll want at least 2-3GB of RAM just for Keycloak if you’re running PostgreSQL on the same box. Folks in the thread also complained about its massive CPU usage compared to Authentik.

---

## 3. Authelia
**Why it’s great**: While not a full-blown IdP in the same way as Authentik or Keycloak, [Authelia](https://www.authelia.com/) punches above its weight. Think of it as a "guardian" for your apps: reverse proxy meets SSO for web apps. It supports OIDC and integrates nicely with Traefik or Nginx.

The best part? It’s lightweight. I deploy this on a Raspberry Pi 4 (4GB model) with no drama. The setup process isn’t too bad if you follow the docs, though YAML errors are inevitable. I mostly use Authelia as a front door to a few critical services like Portainer and Heimdall.

**Who it’s for**: This is perfect if you want basic 2FA + SSO protection without spinning up a full IdP. But if you need multi-tenant support or advanced claims mapping, look elsewhere.

**Quick PSA**: Authelia doesn’t handle OIDC *login* for most third-party apps. If that’s a dealbreaker, skip it.

---

## 4. Vaultwarden
**Why it’s special**: [Vaultwarden](https://github.com/dani-garcia/vaultwarden) (an unofficial Bitwarden server) added OIDC support in version 1.27.0. Yes, the *unofficial* implementation supports modern auth—but the paid Bitwarden does not. Wild, right?

Setting up OIDC in Vaultwarden is surprisingly simple. I’ve paired it with Authentik and Keycloak without issue. The performance is stellar, even on lower-powered hardware. My Vaultwarden box runs on an old Intel NUC with 4GB RAM, and it performs as fast as the hosted Bitwarden for a handful of users.

**What you lose**: No real-time syncing with the official apps. Also, as always with Vaultwarden, you sacrifice some enterprise-level bells and whistles.

---

## 5. Gitea
Yeah, Gitea supports OIDC login too. This was news to me until I needed Git SSO for a one-week experiment (spoiler: I broke half my repos). Connecting Gitea to Authentik or Keycloak wasn’t hard, but the documentation could use some love. Still, it worked right out of the box.

**Drawbacks?** If your Gitea instance is sitting behind Authelia, expect to wrestle with a spaghetti mess of redirects until everything syncs. The r/selfhosted hive mind agrees: SSO support is there, just *finicky*.

---

## Other Notables
- **Nextcloud**: It has an OpenID Connect app that’s free, but it doesn’t play nice with all IdPs. I had mixed results with Authentik.  
- **Jellyfin**: Official docs mention OIDC support, but you’ll need to dig deep into custom config files. A user on r/selfhosted said it "destroyed their parental controls workflow," so proceed with caution.  
- **BookStack**: Native OIDC login. Easy to set up and works like a charm. Not much else to say here.

---

## Final Thoughts
SSO in self-hosting is still a DIY zone. Want full control? Go Authentik or Keycloak. Need lightweight and simple? Authelia. Trying to add SSO to specific apps without a centralized IdP? Vaultwarden or BookStack can handle that. Just don’t expect perfection—it’s all a bit hacky.

And whatever you do, test it *before* you migrate. Debugging curl commands at 1 AM because your token exchange failed is an experience I wouldn’t wish on anyone.

---

### FAQ

#### Can I use Keycloak for small setups?
Yes, but it’s *overkill* for single-user or lightweight environments. Spin up Authentik or Authelia instead.

#### What’s the easiest SSO setup for Jellyfin?
If you’re okay with basic auth, use Authelia. For OIDC, pair Jellyfin with a lightweight IdP like Authentik.

#### Is OIDC the same as OAuth?
Nope. OAuth is about delegation ("give this app partial access"). OIDC adds authentication ("log in to this app"). You’ll use OIDC for SSO.
