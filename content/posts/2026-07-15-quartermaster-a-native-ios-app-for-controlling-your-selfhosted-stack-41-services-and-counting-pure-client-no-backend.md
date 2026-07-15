---
title: "Quartermaster iOS App Review & StepâbyâStep Setup Guide â Control 41+ SelfâHosted Services From Your iPhone"
date: 2026-07-15T08:51:36+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Discover how Quartermaster lets you manage 41+ selfâhosted services on iOS without a backend. Complete setup, prosâcons, and expert verdict inside."
---

## The Community Spark

The r/selfhosted subreddit lit up this week when a user posted **âQuartermaster, a native iOS app for controlling your selfâhosted stack. 41 services (and counting), pure client, no backendâ**. The post hit the front page within hours, racking up more than 12â¯k upâvotes and a flood of comments.  

Why did it explode? Because most selfâhosters still juggle multiple web UIs on a laptop or rely on generic reverseâproxy dashboards that **donât speak iOS natively**. A single, offlineâfirst iPhone app that can talk directly to Docker, Portainer, Home Assistant, and dozens of other services feels like the missing piece of the âanywhereâcontrolâ puzzle.  

The communityâs core question became clear:

> *âCan Quartermaster really replace my laptop dashboard, and how do I get it working without opening security holes?â*

Below we synthesize the Reddit dialogue, verify claims with realâworld testing, and give you a complete, productionâready guide.

---

## Synthesized Community Perspectives

| What Redditors Said | Consensus | Notable CounterâArguments |
|---------------------|-----------|---------------------------|
| **Zeroâbackend design** â the app talks directly to each service using the userâs own authentication tokens. | **Strongly agreed** â praised for privacy and reduced attack surface. | Some warned that âclientâside storage of tokensâ can be risky on a lost phone. |
| **Service coverage (41+)** â includes Docker, Nextcloud, Bitwarden, Gitea, Home Assistant, Plex, and more. | **Generally accepted** â many listed the services they use and confirmed they work. | A few niche services (e.g., Synapse, MinIO) still missing; users requested plugin hooks. |
| **Performance & UI** â native SwiftUI feels snappy, offline caching works. | **Positive** â especially compared to webâbased dashboards that feel clunky on mobile. | Some older iOS devices (iPhoneâ¯6S) reported lag due to heavy JSON parsing. |
| **Security model** â relies on selfâsigned certs and optional 2FA per service. | **Mixed** â securityâsavvy users appreciated the âno central serverâ stance, but wanted clearer guidance on certificate pinning. | A handful suggested adding an optional âgatewayâ container for token rotation. |
| **Pricing** â free for 30 services, $4.99/month for unlimited. | **Acceptable** â most felt the price is justified given the convenience. | A few argued that a fully openâsource alternative would be better for the ethos of selfâhosting. |

**Takeaway:** The community **embraces Quartermasterâs philosophy** (privacyâfirst, clientâonly) but **asks for hardened token storage and clearer TLS guidance**. Our guide incorporates those concerns.

---

## DeepâDive Actionable Guide / Technical Tutorial

Below is a **battleâtested, endâtoâend walkthrough** that you can copyâpaste into your terminal. Weâll assume:

* You have a selfâhosted stack behind a reverse proxy (Caddy or Nginx) with valid DNS.
* Each service exposes a **REST API** or **WebSocket** and supports tokenâbased auth.
* Your iPhone runs iOSâ¯17+.

### 1. Prepare Your Services for Mobile Access

1. **Enable HTTPS with Trusted Certs**  
   ```bash
   # Example using Caddy for automatic TLS
   yourdomain.com {
       reverse_proxy localhost:80
       tls you@example.com
   }
   ```
   *Why?* Quartermaster refuses plainâHTTP connections unless you explicitly toggle âAllow insecureâ. This avoids accidental credential leakage on public WiâFi.

2. **Generate API Tokens**  
   Most services have a âPersonal Access Tokenâ (PAT) feature. Hereâs a quick snippet for Docker (via Portainer) and Home Assistant:

   ```bash
   # Portainer (Docker) â generate token via API
   curl -X POST https://portainer.yourdomain.com/api/auth \
        -H "Content-Type: application/json" \
        -d '{"username":"admin","password":"YOUR_PASSWORD"}' | jq -r .jwt
   # Save the JWT securely (e.g., in 1Password)
   ```

   ```yaml
   # Home Assistant â longâlived access token
   # UI: Settings â People â Your user â Create Token
   ```

   **Tip:** Store each token in a password manager that can export **OTPâcompatible** entries. Quartermaster can import a CSV of `service, url, token`.

### 2. Install Quartermaster on iOS

1. Open the **App Store** â search **âQuartermaster â SelfâHosted Dashboardâ**.  
2. Tap **Get** â **Install** â **Open**.

   *The app size is ~12â¯MB; no background services are installed.*

### 3. Add Your First Service

1. Tap **+ Add Service** â **Select Service Type** (e.g., Docker, Nextcloud).  
2. Fill in:
   * **Base URL** â `https://docker.yourdomain.com`
   * **Auth Method** â *Bearer Token*
   * **Token** â paste the JWT you generated.
3. Toggle **Verify TLS** (default ON). If you use a selfâsigned cert, upload the PEM file under **Settings â Certificates**.

   **Result:** The dashboard instantly shows container status, logs, and start/stop buttons.

### 4. Bulk Import (41+ Services Made Easy)

Quartermaster ships with a **JSON import** format:

```json
[
  {
    "name": "Portainer",
    "type": "docker",
    "url": "https://portainer.yourdomain.com",
    "auth": {
      "method": "bearer",
      "token": "eyJhbGciOi..."
    }
  },
  {
    "name": "Home Assistant",
    "type": "homeassistant",
    "url": "https://ha.yourdomain.com",
    "auth": {
      "method": "bearer",
      "token": "eyJhbGciOi..."
    }
  }
  // â¦ add the rest
]
```

1. Save the file as `quartermaster_import.json` on iCloud Drive.  
2. In the app: **Settings â Import â Choose File**.  
3. Review the autoâdetected services; enable/disable as needed.

### 5. Secure Token Storage on the Device

Quartermaster uses **Appleâs Secure Enclave** to encrypt tokens at rest. To doubleâlock:

1. Enable **Face/Touch ID** under **Settings â Biometric Unlock**.  
2. Turn on **âRequire Passcode after 5â¯min of inactivityâ**.  

If the phone is lost, you can **remoteâwipe** the app data via **Find My iPhone** â **Erase Data**.

### 6. Automations & Widgets

Quartermaster provides **Siri Shortcuts** for common actions:

* **Start/Stop a Docker container**  
  ```text
  Shortcut: "Quartermaster â Start Plex"
  Action: quartermaster://run?service=plex&action=start
  ```

Add the shortcut to the **Home Screen** or **Widget Stack** for oneâtap control.

### 7. Monitoring & Alerts

1. Open **Settings â Alerts**.  
2. Choose **Push**, **Email**, or **Webhook** (e.g., to a Discord channel).  
3. Define thresholds (e.g., CPU > 80â¯% on any container).  

Quartermaster will poll the selected services every **5â¯minutes** (configurable) and fire alerts via Apple Push Notification Service (APNS).

### 8. Troubleshooting Common Pitfalls

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| âUnable to verify TLSâ | Selfâsigned cert not imported | Upload PEM via Settings â Certificates |
| â401 Unauthorizedâ after token rotation | Old token cached | Delete service entry, reâimport fresh token |
| UI freezes on large Docker stacks (100+ containers) | Device memory limit | Enable **Paginated View** under Settings â Display |
| No push notifications | APNS disabled for the app | In iOS Settings â Notifications â Allow Notifications |

---

## Pros & Cons / Comparative Table

| Feature | Quartermaster (iOS) | Portainer (Web UI) | Home Assistant Companion App |
|---------|----------------------|-------------------|------------------------------|
| **Backend Required** | No (pure client) | Yes (Portainer server) | No (direct API) |
| **Service Coverage** | 41+ (Docker, Nextcloud, Bitwarden, etc.) | 1 (Docker) | 1 (Home Assistant) |
| **Offline Mode** | Cached state, actions queue | No | Limited (requires HA) |
| **Security Model** | Token stored in Secure Enclave, optional 2FA per service | Central server stores tokens | Tokens stored on phone, but no extra encryption layer |
| **Pricing** | Free up to 30 services; $4.99/mo unlimited | Free (selfâhosted) | Free |
| **Native iOS UI** | SwiftUI, responsive widgets | Web responsive (Chrome only) | SwiftUI, but limited to HA |
| **Extensibility** | JSON import, custom shortcuts | Plugin system (Docker) | Limited to HA integrations |
| **Learning Curve** | Low â UI wizard + import | Medium â Docker knowledge needed | Low â HA already known |

**Verdict:** For **mobileâfirst operators** who need a single pane of glass across many services, Quartermaster wins on convenience and security. Power users who already run a dedicated dashboard may still prefer Portainer for deep Docker analytics.

---

## The Verdict / Expert Advice

| Persona | Recommendation |
|---------|----------------|
| **Novice SelfâHoster** (â¤5 services) | Start with Quartermasterâs free tier. The guided onboarding eliminates the need to learn each serviceâs UI. |
| **Power User / Sysadmin** (â¥10 services) | Purchase the unlimited plan. Use bulk import + Siri Shortcuts to turn your iPhone into a âremote NOCâ. |
| **SecurityâFocused Enterprise** | Deploy a **gateway container** (e.g., OAuth2âProxy) that rotates tokens daily, then point Quartermaster at the gateway. Combine with MDMâenforced device encryption. |
| **BudgetâConscious Hobbyist** | If youâre comfortable running a small web UI, stick with openâsource Portainer + Home Assistant companion. Quartermaster can still be a complementary mobile layer. |

**Bottom line:** Quartermaster delivers on its promiseâ**control your entire selfâhosted stack from a native iOS app without a backend**âprovided you follow the TLS and tokenâstorage best practices outlined above.

---

## Frequently Asked Questions (FAQ)

**1. Does Quartermaster store my passwords in the cloud?**  
No. All credentials are encrypted locally in the iOS Secure Enclave and never leave the device unless you explicitly export them.

**2. Can I use Quartermaster with services that only support HTTP?**  
Yes, but you must enable the âAllow insecure connectionsâ toggle in Settings. This is discouraged for anything beyond a trusted LAN.

**3. How does the app handle token expiration?**  
When a token returns a 401, Quartermaster marks the service as âAuth neededâ. You can reâenter a fresh token manually or use the builtâin **OAuth2 Refresh** flow for services that support it (e.g., Nextcloud).

**4. Is there an openâsource alternative?**  
A few community projects (e.g., `SelfHostDash`) aim to replicate the UI, but none currently offer the same breadth of native iOS integration or offlineâfirst design as Quartermaster.

---