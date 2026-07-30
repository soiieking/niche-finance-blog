---
title: "The Ultimate Guide to Self-Hosting a Google Voice Alternative in 2026"
date: 2026-07-30T11:06:25+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Ditch Google Voice with our expert guide to self-hosted VoIP alternatives. Learn SIP routing, Docker configurations, and practical community strategies."
---

## The Community Spark

For years, privacy advocates and homelabbers have relied on Google Voice for a free, disposable secondary phone number. But as Google continues to tighten API restrictions and deprecate legacy features, the `r/selfhosted` community is actively asking: *Is there a true, self-hosted Google Voice alternative?*

The consensus? A complete 1:1 drop-in replacement is a myth. The telephony network (PSTN) requires bridging via SIP trunks. However, by combining affordable VoIP providers with open-source PBX software like FreePBX or 3CX, you can build a vastly superior, privacy-respecting telecom hub.

## Synthesized Community Perspectives

Scouring through dozens of `r/selfhosted` threads, a clear consensus emerges: **Self-hosting the PSTN link is a bad idea, but self-hosting the PBX (Private Branch Exchange) routing is the golden path.**

Users universally agree that trying to interface directly with copper lines or cellular gateways is overly complex and unreliable. Instead, the community leverages cheap SIP Trunk providers (like VoIP.ms or Twilio) for the actual phone number, routing the traffic to their self-hosted servers. 

The main debate within the community centers around software choice. Purists favor Asterisk and FreePBX for ultimate granular control. However, many homelabbers argue that 3CX, despite its commercial origins, offers the most frictionless setup. Another secondary option is **JMP.chat**, highly praised for its XMPP-based approach. JMP acts almost like Google Voice, routing SMS and calls through your existing XMPP server, though it requires a paid component.

## Deep-Dive Actionable Guide: Self-Hosting FreePBX via Docker

To establish your own VoIP matrix, the most robust approach is deploying FreePBX in a container. 

### Step 1: Provision a VPS and SIP Trunk
1. Rent a cheap Linux VPS (e.g., Debian/Ubuntu) from a provider that does not block SIP traffic (port 5060).
2. Register with a SIP trunk provider like VoIP.ms. Purchase a DID (Direct Inward Dialing) number.
3. In your VoIP.ms portal, set the SIP URI to point to your VPS IP address: `yourVPS_IP:5060`.

### Step 2: Deploy FreePBX via Docker
Spin up the PBX using a reliable container. Create a `docker-compose.yml` file:

```yaml
version: '3.8'
services:
  freepbx:
    image: tiredofit/freepbx
    container_name: freepbx
    restart: always
    ports:
      - "80:80"
      - "443:443"
      - "5060:5060/udp"
      - "5060:5060"
      - "10000-20000:10000-20000/udp"
    volumes:
      - ./data:/data
    environment:
      - RTP_PORT_START=10000
      - RTP_PORT_END=20000
```

Run `docker-compose up -d`. Access the FreePBX admin panel via `http://<your-VPS-IP>`.

### Step 3: Configure the SIP Trunk in FreePBX
1. Navigate to **Connectivity > Trunks > Add Trunk > SIP (chan_sip)**.
2. **Outbound:** Set the PEER Details using your VoIP.ms credentials (host, username, secret).
3. **Inbound:** Set the User Details with the same credentials.
4. In Connectivity > Inbound Routes, point your newly acquired DID to an extension you've set up on a softphone (like the iOS/Android app Linphone).

## Pros & Cons / Comparative Table

Based on community testing, here is how the self-hosted alternatives stack up against Google Voice:

| Feature | Google Voice | FreePBX + VoIP.ms | 3CX (Self-Hosted) | JMP.chat |
| :--- | :--- | :--- | :--- | :--- |
| **Cost** | Free to start | ~$1-5/month (SIP Trunk) | Free tier up to 3 users | $2.99/month/number |
| **Self-Hosted (Privacy)** | No | Yes | Yes | Partial (XMPP) |
| **SMS Support** | Built-in | Requires SIP Gateway setup | Reliable via SIP trunk | Full (via XMPP) |
| **Maintenance** | Zero | High | Medium | Low |
| **Custom Call Routing**| Extremely limited | Limitless | High | Limited |

## The Verdict / Expert Advice

If you want a true Google Voice alternative that you control, **FreePBX paired with VoIP.ms** is the undisputed champion for technical homelabbers and small businesses. It offers infinite extensibility, high privacy, and very low recurring costs. 

For families or small teams who want the infrastructure of a PBX without the Linux sysadmin headaches, **3CX** is the most viable path. 

If your primary use case is text messaging with voice as an afterthought, **JMP.chat** is the most frictionless, privacy-centric port-in destination for your existing number.

## Frequently Asked Questions (FAQ)

**Can I use Twilio with FreePBX?**
Yes. FreePBX allows you to configure Twilio as a SIP Trunk. Just remember that Twilio charges per minute for inbound and outbound calls, while VoIP.ms often offers unlimited inbound routes for a flat monthly fee.

**Will self-hosting my PBX leak my home IP address?**
No. By hosting the PBX on a cloud VPS, your home IP remains hidden. Your softphone at home connects to the PBX, and all PSTN calls bridge through the VPS IP address.

**Is it difficult to port my existing Google Voice number?**
It is manageable but requires unlocking your Google Voice number. You must request a port-out PIN from Google and then initiate the transfer process with your chosen SIP provider. The process typically takes 3 to 5 business days.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Can I use Twilio with FreePBX?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. FreePBX allows you to configure Twilio as a SIP Trunk. Just remember that Twilio charges per minute for inbound and outbound calls, while VoIP.ms often offers unlimited inbound routes for a flat monthly fee."
      }
    },
    {
      "@type": "Question",
      "name": "Will self-hosting my PBX leak my home IP address?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No. By hosting the PBX on a cloud VPS, your home IP remains hidden. Your softphone at home connects to the PBX, and all PSTN calls bridge through the VPS IP address."
      }
    },
    {
      "@type": "Question",
      "name": "Is it difficult to port my existing Google Voice number?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "It is manageable but requires unlocking your Google Voice number. You must request a port-out PIN from Google and then initiate the transfer process with your chosen SIP provider. The process typically takes 3 to 5 business days."
      }
    }
  ]
}
</script>