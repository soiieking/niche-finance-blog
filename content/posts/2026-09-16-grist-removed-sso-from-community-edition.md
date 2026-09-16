---
title: 'Grist Removed SSO from Community Edition: What Happened and Why It Matters'
date: '2026-09-16 14:00:04+08:00'
draft: false
tags:
- selfhosted
- productivity
- grist
- open-source
summary: Grist yanked SSO from its Community Edition, sparking a debate in the self-hosted
  world. Here's what happened and what you can do.
---

## What Happened: Grist Drops SSO from Community Edition

If you’re running a self-hosted Grist instance and just updated to the latest version (v2.5.0 at the time of writing), you might’ve noticed something missing: Single Sign-On (SSO) support. That’s right — they straight up removed it from the Community Edition. It’s now a paid feature under their hosted plans or the “Team” license.

Cue the collective groan on [r/selfhosted](https://www.reddit.com/r/selfhosted). One of the top comments summed it up well: *“Feels like open-core bait and switch.”* And honestly, they’re not wrong.

This shift has major self-hosting implications, especially if you're managing users across apps. It's not just about convenience—SSO is table-stakes for integrating tools across your stack. Let’s dissect why this change hurts and what options you’ve got now.

## Why This Matters for Self-Hosters

SSO isn’t just a “nice-to-have.” It’s a must if you're maintaining multiple apps and don’t trust your friends or coworkers to remember credentials without texting you 30 times a year. Or if you’re running something for a collective (e.g., a journalist group) and need authentication baked into your workflow. 

Before this update, Community Edition users could set up SSO with OAuth providers like Authentik or Keycloak. The config wasn’t *too* painful — maybe 30 minutes for someone half-decent with YAML and a working reverse proxy. Now? You’re locked out unless you pony up for the Team plan, which, for the record, is $10/user/month. Small orgs can justify it, but it’s absolute overkill for just you and two buddies sharing some docs.

One comment I saw floated the idea of downgrading to v2.4.3 to keep SSO working, but that’s a temporary band-aid at best. Long-term, you’re not getting security updates or feature parity. And let’s be real — hanging onto an old version of a web app is just asking for a CVE nightmare.

## Is This a "Bait-and-Switch"?

The crux of the outrage here is trust. When tools like Grist advertise themselves as open-core, it sets user expectations that the self-hosting experience will remain viable. Yanking a strategic feature like SSO makes people nervous about what’s next. Will they move even more core features behind the paywall? Will “Community Edition” eventually just mean a glorified demo?

One user in the thread pointed out the MongoDB fiasco as a cautionary tale. Mongo went SSPL, lopped off features, and alienated half their dev community. Grist isn't quite there yet, but these moves make it harder to recommend the platform for DIY deployments.

To their credit, the Grist team did communicate the change in their [release notes](https://support.getgrist.com/changelog/#v250-2026-09-01), but that doesn’t make the removal any less painful. Messaging aside, action speaks louder, and this action put a sour taste in many people’s mouths.

## Alternatives: What Can You Use Instead?

If this SSO removal is a dealbreaker, there are strong self-hosted contenders to consider:

1. **Nextcloud**: Not a spreadsheet tool per se, but their **Collectives** and **Tables** app can fill some of the gap. Plus, Nextcloud integrates with LDAP and SSO out of the box.

2. **NocoDB**: A prettier Airtable alternative with a robust open-core model still intact (as of now). It’s lighter on permissions and integrations than Grist, though.

3. **OnlyOffice/Excel Online Self-Hosted**: Overkill unless you’re already knee-deep in Office workflows, but they do support granular user management plus SSO.

Switching tools is rarely fun, and none of these will replace Grist outright if you’re deeply embedded in its formula building. But self-hosters are nothing if not resourceful.

---

## Final Thoughts: Where Do We Go from Here?

Am I uninstalling Grist over this? No, not yet. But I’m concerned. Self-hosted users are already wary of “open-core lite” strategies, and this move adds fuel to that fire. If Grist needed better SSO monetization, they could’ve wall-gardened external integrations for paid tiers while keeping local SSO available. Instead, they went scorched earth.

For now, v2.4.3 works if you’re desperate—though it’s far from ideal. And if you’ve invested heavily in Grist, maybe it’s time to lobby them hard for a middle-ground pricing tier or a DIY option. 

This issue could blow over, or it could be the first domino. Guess we’ll find out.

---

**FAQ**

### What version of Grist removed SSO from the Community Edition?
SSO was removed starting in version 2.5.0, as noted in their September 2026 changelog. If you’re on 2.4.3 or earlier, it’s still supported, but you won’t be getting updates.

### Can you still self-host Grist with the SSO feature?
No, not unless you're willing to pay for the Team plan, which costs $10/user/month. Alternatively, you could stick to v2.4.3, but that's not a sustainable option long-term.

### What are some alternatives to Grist for self-hosted spreadsheets?
Nextcloud (Collectives + Tables), NocoDB, and OnlyOffice are decent choices, but all come with trade-offs depending on your specific use case.
