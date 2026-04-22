---
title: "ServiceNow CIS-EM — Certified Implementation Specialist: Event Management"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-EM, event-management, ITOM, alerts, monitoring, study-guide, job-aid, intermediate]
source: "ServiceNow official Event Management documentation (docs.servicenow.com), Now Learning CIS-EM training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-EM — Certified Implementation Specialist: Event Management

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Event Management |
| Abbreviation | CIS-EM |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | ITOM: Event Management Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; IT operations monitoring concepts |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official Event Management docs:** https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/event-management/concept/c_EventMgmtOverview.html

---

## What CIS-EM Validates

CIS-EM certifies you can **implement and configure ServiceNow Event Management** — ingesting alerts from monitoring tools, correlating events into alerts, mapping them to CMDB CIs, and triggering automated ITSM actions (incidents, changes).

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| Event Management Architecture | Events, alerts, CMDB integration, connectors |
| Event Ingestion | REST API, email parsing, monitoring tool connectors |
| Alert Management | Alert rules, alert correlation, deduplication |
| CMDB Binding | Mapping alerts to CIs |
| Automated Actions | Alert actions, incident creation, notifications |
| Health Log Analytics (HLA) | Log-based event detection |
| Roles & Configuration | EM roles, MID Server for event collection |

---

## Core Concepts — Study & Job Aid

### Event Management Data Flow

```
External Monitoring Tool (Nagios, Datadog, Dynatrace, SolarWinds, etc.)
    ↓ sends raw alert/event data
Event Collector (via REST API, JDBC, email, or connector)
    ↓
em_event table (raw events — can be high volume, short retention)
    ↓ Event Rules applied
em_alert table (deduplicated, correlated alerts — long retention)
    ↓ CMDB binding
Configuration Item (server, app, service)
    ↓ Alert Actions
ITSM (Incident, Change) created automatically
```

**Key tables:**
| Table | Purpose |
|---|---|
| `em_event` | Raw incoming events (high volume, short retention) |
| `em_alert` | Processed, deduplicated alerts (primary working table) |
| `em_alert_rule` | Rules for processing events into alerts |

---

### Event vs Alert

| Concept | Definition |
|---|---|
| Event | A single raw notification from a monitoring tool |
| Alert | A processed, deduplicated representation of a condition on a CI |

Multiple repeated events for the same issue → **deduplicated into one Alert**.
The Alert persists and updates (count, severity) until the condition is resolved.

---

### Event Ingestion Methods

**REST API (most common):**
```
POST /api/global/em/inbound_event
Headers: Content-Type: application/json
Body: {
  "source": "Datadog",
  "node": "web01.company.com",
  "resource": "CPU",
  "severity": "1",
  "description": "CPU utilisation above 95%"
}
```

**Monitoring connectors:**
- Pre-built connectors for: Nagios, SolarWinds, IBM Tivoli, HP OpenView, Dynatrace, Datadog
- Navigator: Event Management → Integrations → Monitoring Connectors

**Email parsing:**
- Parse monitoring alert emails into events using Email Inbound Action scripts

---

### Alert Rules

```
Navigator: Event Management → Administration → Alert Management Rules
```

Alert Rules define how raw events are:
1. **Filtered** — only process events matching specific criteria
2. **Deduplicated** — group repeated events into one alert
3. **Correlated** — group related alerts into one parent alert

**Deduplication example:**
- Rule: if `source` + `node` + `resource` are the same → update the existing alert count rather than creating a new alert
- Prevents alert storms (hundreds of events creating hundreds of alerts)

**Correlation example:**
- Rule: if 5 child alerts all relate to the same CI → create one parent "Service Degradation" alert

---

### CMDB Binding (Alert to CI Mapping)

Binding maps an incoming alert to the correct CI in the CMDB.

```
Navigator: Event Management → Administration → CI Lookup Rules
```

**Lookup fields (in order of preference):**
| Field | Example |
|---|---|
| IP Address | `10.0.1.5` |
| Hostname (node field) | `web01.company.com` |
| DNS Name | FQDN lookup |
| Name | CI name field |

**Binding result:**
- Alert's `cmdb_ci` field populated with the matching CI
- Alert inherits CI's service relationships → impacts are shown on service map
- Unbound alerts: CMDB binding failed — CI not found or not yet in CMDB

---

### Alert Actions (Automated Response)

```
Navigator: Event Management → Administration → Alert Action Rules
```

Alert Actions trigger automatically when alert conditions are met:

| Action Type | Example Trigger | Result |
|---|---|---|
| Create Incident | Alert severity = Critical | Incident created and linked to alert |
| Create Change | Scheduled maintenance window alert | Change request created |
| Run Script | Any alert type | Custom script execution |
| Send Notification | Alert escalated | Email/Slack notification |
| Update Existing Incident | Alert severity changes | Update incident priority |

**Auto-close:**
- When monitoring tool sends a "resolve" event → alert severity set to OK → auto-close the linked incident

---

### Severity Levels

| Severity Value | Meaning |
|---|---|
| 1 | Critical |
| 2 | Major |
| 3 | Minor |
| 4 | Warning |
| 5 | OK / Resolved |

OK/Resolved (severity 5) events signal the condition has cleared — used to auto-close alerts and incidents.

---

### Health Log Analytics (HLA)

Optional add-on that applies **machine learning** to log data to detect anomalies and generate events.

```
Navigator: Health Log Analytics → Dashboard
```

- Ingests logs from Logstash, Splunk, Elasticsearch
- ML models detect patterns that correlate with future incidents
- Generates predictive alerts before users are impacted

---

### Event Management Roles

| Role | Access |
|---|---|
| `evt_mgmt_user` | View events and alerts |
| `evt_mgmt_operator` | Manage alerts, create incidents from alerts |
| `evt_mgmt_admin` | Full EM configuration and administration |

---

## Common Exam Scenarios

**Scenario 1:** Your monitoring tool sends 500 events per minute for the same server CPU issue. The ServiceNow instance shows 500 individual alerts. How do you fix this?
→ Configure an **Alert Deduplication Rule** — match on `source + node + resource` to consolidate repeat events into a single alert with an incrementing count.

**Scenario 2:** An incoming alert has `node = db01.company.com` but no CI is being bound. What should you check?
→ The **CI Lookup Rule** — verify that hostname lookup is enabled. Also check that the CI `db01.company.com` exists in the CMDB with a matching name or DNS field.

**Scenario 3:** The team wants critical database alerts to automatically create P1 incidents. How do you configure this?
→ Create an **Alert Action Rule** — condition: `severity = 1 (Critical)` AND `cmdb_ci.class = Database` → Action: Create Incident with priority P1.

**Scenario 4:** A monitoring tool resolves an alert, but the linked incident is not being closed automatically. What should you verify?
→ The monitoring tool should send a resolve event with `severity = 5 (OK)`. Verify the **auto-close Alert Action Rule** exists — condition: severity = OK → Action: Close linked incident.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — Event Management path** | https://nowlearning.servicenow.com — ITOM: Event Management Fundamentals |
| **ServiceNow Docs — Event Management** | https://docs.servicenow.com — search "Event Management" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-Disco — Discovery (CMDB populated before EM binding works)
- ServiceNow-CIS-ITSM — ITSM (incident/change created by EM Alert Actions)

---

## References

- ServiceNow Docs — Event Management: https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/event-management/concept/c_EventMgmtOverview.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]