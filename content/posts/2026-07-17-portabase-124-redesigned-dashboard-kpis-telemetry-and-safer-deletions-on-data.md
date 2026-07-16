---
title: "Portabase 1.24 Unveiled: Master Dashboard Redesign, Enhanced Safety, and Telemetry for Self-Hosted Power Users"
date: 2026-07-17T01:53:05+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Discover Portabase 1.24's redesigned dashboard KPIs, telemetry insights, and irreversible deletion safeguards for self-hosted database mastery."
---

## The Community Spark: Why Portabase 1.24 Matters

The **r/selfhosted** community recently erupted with excitement over Portabase 1.24âs release, particularly praising its **redesigned dashboard KPIs**, **telemetry features**, and **safer deletion workflows**. These updates address common pain points: lack of actionable insights from data, accidental data loss, and opaque performance metrics. With 1.24, Portabase aims to bridge the gap between user-friendly simplicity and enterprise-grade reliabilityâa hot topic for home labs and small dev teams.

---

## Synthesized Community Perspectives

### Consensus: Dashboard KPIs Are a Game-Changer
The new KPI dashboard, featuring real-time data ingestion rates and storage utilization graphs, was overwhelmingly praised. As u/DevOpsDrew noted, *âFinally, a dashboard that tells me whatâs critical without drowning in logs.â* Developers lauded the ability to export KPI data for external analytics tools like Grafana.

### Debate: TelemetryâPrivacy vs. Performance
Telemetry in 1.24 sparked heated debate. While u/LinuxNerd88 called it âa godsend for debugging edge cases,â privacy-focused users like u/SecWorrier demanded optional opt-in controls. The community consensus? **Telemetry is valuable but should be opt-in with clear configuration paths**.

### Unified Praise for Safer Deletions
The âsoft deleteâ workflowâadding a 7-day recovery windowâwas universally celebrated. As u/DataLossPhobia shared, *âThis feature alone is worth upgrading for. Iâve accidentally dropped databases before.â*

---

## Deep-Dive Actionable Guide: Configuring Portabase 1.24

### Step 1: Enable/Configure Telemetry
To toggle telemetry, edit your `config.yaml`:
```yaml
telemetry:
  enabled: true
  endpoint: "https://telemetry.portabase.dev"
  redact_ips: true # Optional IP anonymization
```
Restart the service:
```bash
sudo systemctl restart portabase
```

### Step 2: Customize Dashboard KPIs
Access the admin UI â **Settings** â **Dashboard** to add/remove KPI widgets. For programmatic control, use the API:
```bash
curl -X POST http://localhost:8080/api/v1/dashboard/config \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{"widgets":["storage_usage","query_throughput"]}'
```

### Step 3: Set Up Soft Deletes
Modify the soft delete retention period:
```yaml
data_retention:
  soft_delete_days: 7
```

---

## Pros & Cons: Portabase 1.24 Features

| Feature                  | Pros                                      | Cons                                      |
|--------------------------|-------------------------------------------|-------------------------------------------|
| Dashboard KPIs           | Real-time insights, exportable metrics     | Mild UI clutter for advanced users        |
| Telemetry                | Improved troubleshooting, performance logs | Requires trust for data transmission      |
| Soft Deletes              | Prevents accidental data loss              | Adds 7-day delay for permanent cleanup    |

---

## The Verdict: Who Should Upgrade?

- **Upgrade Now**: Dev teams needing performance insights; users prone to accidental deletions.
- **Wait & Assess**: Privacy-first orgs should review the telemetry protocol first.
- **Hybrid Setup**: Run 1.24 in staging to test compatibility with custom plugins.

---

## Frequently Asked Questions

**1. How do I disable telemetry entirely?**  
Set `enabled: false` in the `config.yaml` telemetry block and restart Portabase.

**2. Can I recover data after the soft delete period?**  
No. Data is permanently deleted after the retention window. Use backups.

**3. Is the dashboard customizable for non-technical users?**  
Yesâprebuilt templates simplify configuration without YAML edits.

**4. Does Portabase 1.24 support Prometheus metrics?**  
Yes, via the new `/metrics` endpoint accessible to Prometheus scrapers.

---

```json
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "How do I disable telemetry entirely?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Set `enabled: false` in the `config.yaml` telemetry block and restart Portabase."
      }
    },
    {
      "@type": "Question",
      "name": "Can I recover data after the soft delete period?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No. Data is permanently deleted after the retention window. Use backups."
      }
    },
    {
      "@type": "Question",
      "name": "Is the dashboard customizable for non-technical users?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yesâprebuilt templates simplify configuration without YAML edits."
      }
    },
    {
      "@type": "Question",
      "name": "Does Portabase 1.24 support Prometheus metrics?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, via the new `/metrics` endpoint accessible to Prometheus scrapers."
      }
    }
  ]
}
</script>