---
title: "ServiceNow CIS-DF — Certified Implementation Specialist: Data Foundations (CMDB & CSDM)"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-DF, CMDB, CSDM, data-foundations, study-guide, job-aid, intermediate]
source: "ServiceNow official CMDB documentation (docs.servicenow.com), Now Learning CIS-DF training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🗄 ServiceNow CIS-DF — Certified Implementation Specialist: Data Foundations (CMDB & CSDM)

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Data Foundations (CMDB & CSDM) |
| Abbreviation | CIS-DF |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | CMDB Fundamentals + CSDM Fundamentals (Now Learning) |
| Note | New mainline certification — announced Knowledge 2026. Replaces previous CMDB-focused content from other cert tracks. |
| Recommended experience | ServiceNow administration; ITSM/ITOM implementation experience helpful |

**Official training path:** https://nowlearning.servicenow.com — search "Data Foundations"
**Official CMDB docs:** https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/configuration-management/concept/c_ITILConfigurationManagement.html

> ⚑ CIS-DF is a new certification (2026). Verify the current exam blueprint at nowlearning.servicenow.com — domain weights and question format may evolve in early releases.

---

## What CIS-DF Validates

CIS-DF certifies that you can **implement and maintain the ServiceNow CMDB (Configuration Management Database) and apply the Common Service Data Model (CSDM)** — the foundational data layer that every ServiceNow application depends on.

A well-implemented CMDB and CSDM enables: accurate ITSM impact analysis, reliable Vulnerability Response, effective IT Asset Management, and correct Security Operations routing.

---

## Why CMDB Matters (Context for Other Certs)

```
CMDB is the foundation for:
├── ITSM (Incident/Change CI impact analysis)
├── VR (Vulnerability Response — CIs are the assets being scanned)
├── SIR (Security Incident — affected CI context)
├── ITAM (Asset lifecycle — physical/virtual assets as CIs)
├── ITOM Discovery (auto-populates CMDB from scans)
└── Service Mapping (connects CIs into business services)
```

Every cert you hold that involves CIs relies on CMDB data quality.

---

## Core Concepts — Study & Job Aid

### CMDB Structure

The CMDB stores **Configuration Items (CIs)** — records representing infrastructure, applications, services, and relationships.

```
Root table: cmdb  (all CI classes extend this)
Base CI class: cmdb_ci

Common CI classes (examples):
cmdb_ci_computer         → computers / servers
cmdb_ci_server           → servers specifically
cmdb_ci_win_server       → Windows servers (extends cmdb_ci_server)
cmdb_ci_linux_server     → Linux servers (extends cmdb_ci_server)
cmdb_ci_appl             → applications
cmdb_ci_database         → databases
cmdb_ci_network_adapter  → network interfaces
cmdb_ci_service          → business services
cmdb_ci_ip_router        → network routers
```

**All CI classes inherit from `cmdb_ci`** — so they all have: Name, Class, Serial Number, IP Address, Operational Status, Managed By, etc.

> Source: [ServiceNow Docs — CMDB](https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/configuration-management/concept/c_ITILConfigurationManagement.html)

---

### CI Relationships

CIs are connected via **Relationship** records (`cmdb_rel_ci`).

```
Table: cmdb_rel_ci
Fields: Parent CI, Child CI, Type (relationship type)

Relationship types (examples):
- Hosted on / Hosts
- Runs on / Runs
- Depends on / Used by
- Contains / Contained by
- Connects to / Connected by
- Virtualized by / Virtualizes
```

**View relationships:**
```
On a CI record → Relationships tab → shows parent/child CIs
OR: CI record → Map button → visual relationship map
```

**Create a relationship:**
```
On a CI record → Relationships tab → Add Relationship
→ select Parent/Child, Relationship Type, related CI
```

---

### Discovery

ServiceNow **Discovery** automatically scans the network and populates the CMDB with CIs.

```
Navigator: Discovery → Discovery Schedules → New
```

**Discovery components:**

| Component | Purpose |
|---|---|
| MID Server | Java agent deployed in the network — performs scans; communicates with ServiceNow |
| Discovery Schedule | Defines when and what IP ranges to scan |
| Probe | A script that runs against a discovered device to collect data |
| Sensor | Parses probe output and maps data to CMDB fields |
| Pattern | Discovery content for specific CI types (server, database, application) |

**MID Server:**
```
- Deployed as a Windows service or Linux daemon in your network
- Communicates outbound to ServiceNow on HTTPS port 443
- No inbound firewall rules needed (outbound only)
- Multiple MID Servers for redundancy and segmentation
Navigator: MID Server → Servers → your MID Server record
```

**Discovery Credentials:**
```
Navigator: Discovery → Credentials → New
Types: Windows (WMI), SSH (Linux/Unix), SNMP, VMware, Database, REST
Credentials stored encrypted in ServiceNow — never in plaintext
```

> Source: [ServiceNow Docs — Discovery](https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/discovery/concept/c_DiscoveryApplication.html)

---

### CI Identification and Reconciliation

When Discovery (or any import) creates/updates CIs, ServiceNow uses **CI Identification and Reconciliation** to prevent duplicates.

**IRE (Identification and Reconciliation Engine):**
```
Navigator: CI Class Manager → or Configuration → Identification / Reconciliation
```

- **Identifier rules** — define which fields uniquely identify a CI (e.g. serial number + class)
- **De-duplication** — merges duplicate CIs based on identifier matches
- **Reconciliation** — when multiple sources update the same CI field, reconciliation rules determine which source wins (precedence)

**Data sources and precedence:**
```
Common data sources: Discovery, ServiceNow Agent, SCCM, CSV import, manual
Precedence order configured per-field per-class
Higher precedence = wins when conflict occurs
```

---

### CMDB Health

**CMDB Health** measures the quality and completeness of your CMDB data.

```
Navigator: Configuration → CMDB Health Dashboard
OR: CMDB Health → Run Health Checks
```

**Health dimensions:**
| Dimension | What it measures |
|---|---|
| Completeness | Required fields populated |
| Compliance | CIs follow CSDM model |
| Correctness | Data is accurate (relationships valid, no orphaned records) |
| Staleness | CIs not updated recently — may be decommissioned |
| Duplicates | Multiple CI records representing the same asset |

**CMDB Health Simulator** — available on Now Learning for practice.

> Source: [ServiceNow Docs — CMDB Health](https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/configuration-management/concept/c_CMDBHealth.html)

---

### Common Service Data Model (CSDM)

The **Common Service Data Model (CSDM)** is ServiceNow's prescriptive framework for organising CMDB data into a standardised hierarchy aligned to business services.

**CSDM hierarchy (simplified):**
```
Business Capability
└── Business Application      (the app from a business perspective)
    └── Application Service   (the technical service running the app)
        └── Service Offering  (what's offered to consumers)
            └── Consumed by: Users / Business Units
```

**Key CSDM tables:**

| Table | Purpose |
|---|---|
| `cmdb_ci_business_app` | Business Applications |
| `cmdb_ci_service_discovered` | Technical (Application) Services discovered by Service Mapping |
| `cmdb_ci_service` | Business Services (manually defined) |
| `service_offering` | Service Offerings linked to Business Apps |

**Why CSDM matters:**
- Provides the common language used across ITSM, VR, SIR, ITAM
- Ensures a P1 Incident on a CI automatically shows which Business Application is affected
- Enables "service impact" views for executives

> Source: [ServiceNow Docs — CSDM](https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/csdm/concept/csdm-overview.html)

---

### CI Lifecycle Management

```
Operational Status values on cmdb_ci:
1 = Operational
2 = Non-Operational
3 = Repair in Progress
4 = DR Standby
5 = Ready
6 = Retired
7 = Pipeline
8 = Catalog
```

**Decommissioning a CI:**
```
1. Update Operational Status → Retired
2. Remove from active relationships
3. Close any open Incidents/Changes referencing this CI
4. Archive or delete record per retention policy
```

---

### Service Graph Connectors

**Service Graph Connectors** are certified integration templates for populating the CMDB from third-party sources (SCCM, Intune, AWS, Azure, GCP, etc.).

```
Navigator: Service Graph Connectors (available via ServiceNow Store)
```

Unlike generic Import Sets, Service Graph Connectors use the IRE and are designed to maintain CMDB health automatically.

> Source: [ServiceNow Docs — Service Graph Connectors](https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/configuration-management/concept/c_ServiceGraphConnectors.html)

---

### CMDB Administration Tasks

**View all CIs of a class:**
```
Navigator: Configuration → [CI Class] → All
e.g. Configuration → Servers → All
```

**Run a manual Discovery:**
```
Navigator: Discovery → Discovery Schedules → select schedule → Discover Now
```

**Check MID Server health:**
```
Navigator: MID Server → Servers → select MID Server → Validate button
Status: Up = healthy, Down = not reachable
```

**Find orphaned CIs (no relationships):**
```
Navigator: Configuration → CMDB Health → run Correctness check
Orphaned CIs listed in results
```

**Merge duplicate CIs:**
```
Navigator: Configuration → CMDB → Duplicate CI Remediation
Select duplicates → Merge → choose master record
```

---

## Common Exam Scenarios

**Scenario 1:** After a Discovery run, new server CIs are being created as duplicates alongside existing records. What should you configure to prevent this?
→ Review and configure **Identifier Rules** in CI Identification — ensure the serial number or hostname field is set as the unique identifier for the server class.

**Scenario 2:** A security team is reporting that Vulnerability Response items cannot be linked to CIs because scanner hostnames don't match CMDB CI names. What do you configure?
→ **CI Identification matching rules** in Vulnerability Response Administration — map scanner hostname format to the CI name field.

**Scenario 3:** CMDB Health shows a high percentage of stale CIs that haven't been updated in 180 days. What is the recommended approach?
→ Schedule a **Discovery** run to refresh those CIs, and implement a **staleness policy** with automated decommission workflows for CIs not found after N consecutive scans.

**Scenario 4:** A business executive wants to see which business applications are impacted when a server goes down. What CSDM configuration enables this?
→ The server CI must be linked via relationships to an **Application Service**, which is linked to a **Business Application** following the CSDM model. Impact propagates up the chain.

**Scenario 5:** Multiple data sources (Discovery, SCCM, manual) are overwriting each other's CI updates. How do you control which source wins?
→ Configure **Reconciliation Rules** (data source precedence) in CI Class Manager — set Discovery as the highest precedence source for infrastructure fields.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — CMDB path** | https://nowlearning.servicenow.com — CMDB Fundamentals + CSDM Fundamentals (required) |
| **ServiceNow Docs — CMDB** | https://docs.servicenow.com — search "CMDB" or "Configuration Management" |
| **ServiceNow Docs — CSDM** | https://docs.servicenow.com — search "Common Service Data Model" |
| **CMDB Health Simulator** | Available on Now Learning |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Tables, fields, platform fundamentals
- ServiceNow-CIS-ITSM — CMDB used in Incident, Change, Problem
- ServiceNow-CIS-VR — Vulnerability Response depends on CMDB CIs
- ServiceNow-CIS-SIR — Security Incident Response uses CI context

---

## References

- ServiceNow Docs — CMDB: https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/configuration-management/concept/c_ITILConfigurationManagement.html
- ServiceNow Docs — Discovery: https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/discovery/concept/c_DiscoveryApplication.html
- ServiceNow Docs — CSDM: https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/csdm/concept/csdm-overview.html
- ServiceNow Docs — CMDB Health: https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/configuration-management/concept/c_CMDBHealth.html
- ServiceNow Docs — Service Graph Connectors: https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/configuration-management/concept/c_ServiceGraphConnectors.html
- Knowledge 2026 Certification (confirms CIS-DF as new mainline cert): https://servicenow.swoogo.com/knowledge26certification/10206927
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]