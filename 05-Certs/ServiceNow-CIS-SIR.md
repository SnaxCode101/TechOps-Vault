---
title: "ServiceNow CIS-SIR — Certified Implementation Specialist: Security Incident Response"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-SIR, security-incident-response, SecOps, study-guide, job-aid, intermediate]
source: "ServiceNow official SIR documentation (docs.servicenow.com), Now Learning CIS-SIR training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🔐 ServiceNow CIS-SIR — Certified Implementation Specialist: Security Incident Response

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Security Incident Response |
| Abbreviation | CIS-SIR |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | Security Operations Fundamentals + Security Incident Response Implementation (Now Learning) |
| Recommended experience | ServiceNow admin/implementation experience; security operations background helpful |

**Official SIR training path:** https://nowlearning.servicenow.com
**Official SIR docs:** https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/concept/security-incident-response.html

---

## What CIS-SIR Validates

CIS-SIR certifies that you can **implement, configure, and maintain the Security Incident Response (SIR) application** on ServiceNow. SIR automates security incident workflows, integrates with SIEM tools, manages threat intelligence, and coordinates response across security teams.

> Source: [ServiceNow Docs — Security Incident Response](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/concept/security-incident-response.html)

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam. The domains below reflect the SIR implementation curriculum.

| Domain | Key Topics |
|---|---|
| SIR Overview & Architecture | SIR data model, Security Incident table, relationship to ITSM Incident |
| SIEM Integration | Configuring event ingest from SIEM tools (Splunk, IBM QRadar, Microsoft Sentinel) |
| Security Incident Lifecycle | States, workflows, prioritisation, assignment, containment, closure |
| Threat Intelligence | Threat sources, indicators of compromise (IOCs), enrichment |
| Playbooks & Automation | Configuring playbooks, Flow Designer in SIR, automated response |
| Configuration & Administration | SIR roles, properties, queues, grouping rules |
| Reporting & Dashboards | SIR metrics, SLA, performance analytics |
| Integration with ITSM | Relationship between Security Incidents and ITSM records |

---

## Core Concepts — Study & Job Aid

### SIR Application Overview

Security Incident Response (SIR) is a ServiceNow Security Operations (SecOps) application that manages **security incidents** — events detected by security tools (SIEM, EDR, IDS) that require investigation and response.

**SIR is distinct from ITSM Incident Management:**

| | SIR Security Incident | ITSM Incident |
|---|---|---|
| Table | `sn_si_incident` | `incident` |
| Purpose | Security threat response | IT service restoration |
| Triggers | SIEM alerts, threat intel | User reports, monitoring |
| Audience | SOC analysts, security team | IT service desk |
| Lifecycle | Detect → Analyse → Contain → Eradicate → Recover | New → In Progress → Resolved → Closed |

> Source: [ServiceNow Docs — SIR overview](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/concept/security-incident-response.html)

---

### Key SIR Tables

| Table | Purpose |
|---|---|
| `sn_si_incident` | Security Incident records |
| `sn_si_task` | Security Incident Tasks (sub-tasks of a security incident) |
| `sn_si_observable` | Observables — IPs, hashes, domains, URLs attached to incidents |
| `sn_ti_ioc` | Indicators of Compromise (from threat intelligence feeds) |
| `sn_ti_threat_source` | Threat intelligence source configurations |
| `sn_si_event` | Events ingested from SIEM tools before becoming incidents |

---

### Security Incident Lifecycle

```
States in sn_si_incident (default):
Draft → Analysis → Contain → Eradicate → Recover → Review → Closed

Key transitions:
- Draft → Analysis: when an event is confirmed as a true positive
- Analysis → Contain: when scope is understood; containment actions begin
- Contain → Eradicate: threat contained; begin removal
- Eradicate → Recover: threat removed; restore normal operations
- Recover → Review: post-incident review / lessons learned
- Review → Closed: review complete
```

**Priority / Severity:**
- Priority is calculated from **Urgency × Impact** (same matrix as ITSM)
- SIR also has a **Severity** field specific to the security context (Critical/High/Medium/Low)
- Configurable via: SIR Properties or via Priority Lookup Rules

---

### Configuring SIEM Integration

SIR ingests events from SIEM tools via **Event Management** or direct REST API ingest.

```
Navigator: Security Incident Response → Administration → SIEM Integrations
OR via: Integration Hub spokes for specific SIEM tools
```

**Supported ingest methods:**
1. **Event Management connector** — SIEM pushes alerts into ServiceNow Event Management; SIR auto-creates Security Incidents from qualifying events
2. **Direct REST ingest** — SIEM calls ServiceNow REST API to create `sn_si_incident` records directly
3. **Integration Hub spokes** — pre-built spokes for Splunk, QRadar, Microsoft Sentinel, etc.

**Grouping Rules** — prevent alert floods from creating thousands of individual incidents:
```
Navigator: Security Incident Response → Administration → Event Grouping Rules
- Group by: source IP, alert type, time window
- Max events per incident: configurable
- Creates one Security Incident from N related events
```

> Source: [ServiceNow Docs — SIR event ingest](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/task/t_CreateSecurityIncidentsFromEvents.html)

---

### Playbooks in SIR

Playbooks are structured response workflows attached to Security Incidents. They guide analysts through investigation and response steps.

```
Navigator: Security Incident Response → Playbooks
OR: Process Automation Designer → Playbooks
```

**Playbook components:**
- **Stages** — phases of the response (e.g. Triage, Contain, Eradicate)
- **Activities** — tasks within each stage
- **Conditions** — logic that determines which path to follow
- **Automation** — Flow Designer flows triggered at each activity

**Attach a playbook to a Security Incident type:**
```
Navigator: Security Incident Response → Administration → Incident Types
Select a type → Playbook field → assign playbook
```

> Source: [ServiceNow Docs — SIR Playbooks](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/concept/sir-playbooks.html)

---

### Observables and IOC Enrichment

**Observables** are pieces of evidence attached to a Security Incident — IP addresses, file hashes, domains, URLs, email addresses.

```
On a Security Incident form → Observables related list → Add Observable
Types: IP Address, Domain, URL, File Hash, Email Address, Username
```

**Enrichment:** SIR can automatically query threat intelligence sources against observables:
- Query VirusTotal, Recorded Future, MISP, or custom feeds
- Enrichment adds reputation scores and threat context to each observable
- Configure: SIR → Threat Intelligence → Sources

**IOC Matching:** When a known IOC from a threat feed matches an observable on an incident, SIR flags the match automatically — allowing analysts to quickly identify known threats.

> Source: [ServiceNow Docs — Observables](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/concept/c_Observables.html)

---

### SIR Roles

| Role | Access |
|---|---|
| `sn_si.admin` | Full SIR administration — configure everything |
| `sn_si.analyst` | Create, update, and close Security Incidents |
| `sn_si.reader` | Read-only access to Security Incidents |
| `sn_si.manager` | Manage SIR queues and assignments |

> Source: [ServiceNow Docs — SIR roles](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/reference/r_SIRRoles.html)

---

### SIR Key Properties

```
Navigator: Security Incident Response → Administration → Properties

Key properties:
- sn_si.default_assignment_group  → default group for new incidents
- sn_si.incident_auto_close       → auto-close incidents after N days resolved
- sn_si.enable_threat_intelligence → enable/disable TI enrichment
- sn_si.playbook_auto_attach      → auto-attach playbooks based on incident type
```

---

### SIR Administration — Day-to-Day Tasks

**Configure an Incident Type:**
```
Navigator: SIR → Administration → Incident Types → New
Fields: Name, Category, Default Priority, Default Assignment Group, Playbook
```

**Configure Assignment Rules (auto-routing):**
```
Navigator: SIR → Administration → Assignment Rules
Set conditions (e.g. category = phishing) → assign to specific group/analyst
```

**Manage SIR Queues:**
```
Navigator: SIR → Queues
Create queues for different security team tiers (L1 Triage, L2 Analysis, L3 Response)
Assign groups to queues
```

**Configuring Threat Intelligence Sources:**
```
Navigator: SIR → Threat Intelligence → Sources → New
Types: TAXII feed, REST API feed, MISP, VirusTotal, Recorded Future
Configure polling schedule and field mappings
```

---

### Integration with ITSM

SIR Security Incidents can be **linked to ITSM records**:
- A Security Incident requiring infrastructure changes → create a linked **ITSM Change Request**
- A Security Incident caused by a system failure → link to an **ITSM Incident**

```
On Security Incident form → Related Records tab
→ Create or link: Incident, Change Request, Problem
```

This keeps security response coordinated with IT operations.

---

### Reporting and Metrics

```
Navigator: SIR → Reports (pre-built)
Key reports:
- Open Security Incidents by Severity
- Mean Time to Detect (MTTD)
- Mean Time to Respond (MTTR)
- Incidents by Category
- Analyst workload / queue depth

SLA configuration:
Navigator: SIR → Administration → SLA Definitions
```

---

## Common Exam Scenarios

**Scenario 1:** A SIEM is generating 500 alerts per hour. How do you prevent SIR from creating 500 separate Security Incidents?
→ Configure **Event Grouping Rules** to aggregate related events into a single Security Incident.

**Scenario 2:** A Security Incident for a phishing campaign needs a structured step-by-step response guide. What feature provides this?
→ **Playbooks** — attach a phishing playbook to the phishing incident type.

**Scenario 3:** An analyst needs to check if an IP address in a Security Incident is a known threat. What SIR feature handles this automatically?
→ **Observable enrichment** with a configured Threat Intelligence source.

**Scenario 4:** The security team needs a containment change implemented by the network team. What should they do in SIR?
→ Link a **ITSM Change Request** to the Security Incident via the Related Records tab.

**Scenario 5:** An analyst can view Security Incidents but cannot update them. What role do they need?
→ `sn_si.analyst` — the `sn_si.reader` role is read-only.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — SIR path** | https://nowlearning.servicenow.com — Security Operations Fundamentals + SIR Implementation (required) |
| **ServiceNow Docs — SIR** | https://docs.servicenow.com — search "Security Incident Response" |
| **ServiceNow Developer Portal** | https://developer.servicenow.com — request a PDI with SecOps plugin |
| **ServiceNow Community — SecOps** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CIS-VR — Vulnerability Response (companion SecOps application)
- ServiceNow-CSA — Platform fundamentals
- Incident-Response — General IR methodology (NIST SP 800-61)
- Threat-Intelligence — Threat intelligence fundamentals

---

## References

- ServiceNow Docs — Security Incident Response: https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/concept/security-incident-response.html
- ServiceNow Docs — SIR Roles: https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/reference/r_SIRRoles.html
- ServiceNow Docs — SIR Playbooks: https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/concept/sir-playbooks.html
- ServiceNow Docs — Observables: https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/concept/c_Observables.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]