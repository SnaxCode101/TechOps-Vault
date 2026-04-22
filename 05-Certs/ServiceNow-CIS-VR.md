---
title: "ServiceNow CIS-VR — Certified Implementation Specialist: Vulnerability Response"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-VR, vulnerability-response, SecOps, study-guide, job-aid, intermediate]
source: "ServiceNow official VR documentation (docs.servicenow.com), Now Learning CIS-VR training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🛡 ServiceNow CIS-VR — Certified Implementation Specialist: Vulnerability Response

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Vulnerability Response |
| Abbreviation | CIS-VR |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | Security Operations Fundamentals + Vulnerability Response Implementation (Now Learning) |
| Recommended experience | ServiceNow admin experience; vulnerability management / security background helpful |

**Official VR training path:** https://nowlearning.servicenow.com
**Official VR docs:** https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/concept/vulnerability-response.html

---

## What CIS-VR Validates

CIS-VR certifies that you can **implement, configure, and operate the Vulnerability Response (VR) application** on ServiceNow — including scanner integrations, vulnerability grouping, remediation workflows, SLA tracking, and reporting.

> Source: [ServiceNow Docs — Vulnerability Response](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/concept/vulnerability-response.html)

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| VR Overview & Architecture | VR data model, key tables, relationship to CMDB and ITSM |
| Scanner Integration | Qualys, Tenable, Rapid7, NVD integration; data import |
| Vulnerability Items & Groups | Vulnerable Items, Vulnerability Groups, deduplication |
| Remediation Workflows | Remediation tasks, Change Request integration, SLAs |
| Configuration Compliance | Configuration Compliance module, test results |
| Administration | VR roles, properties, exception handling, SLA definitions |
| Reporting & Metrics | Vulnerability dashboards, risk scores, aging reports |
| CMDB Integration | How VR uses CMDB CI data for context and routing |

---

## Core Concepts — Study & Job Aid

### VR Application Overview

Vulnerability Response (VR) manages the lifecycle of vulnerabilities found on infrastructure — from scanner import through remediation closure. It integrates with vulnerability scanners, the CMDB, and ITSM Change Management.

**VR workflow at a high level:**
```
Scanner runs scan → Results imported to ServiceNow
→ Vulnerable Items created (one per finding per CI)
→ Items grouped into Vulnerability Groups (by CVE, by asset group, etc.)
→ Remediation tasks / Change Requests created and assigned
→ Teams remediate → scanner rescans → items auto-closed when fixed
→ SLAs tracked throughout lifecycle
```

> Source: [ServiceNow Docs — VR overview](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/concept/vulnerability-response.html)

---

### Key VR Tables

| Table | Purpose |
|---|---|
| `sn_vul_vulnerable_item` | Vulnerable Items — one record per vulnerability per CI |
| `sn_vul_entry` | Vulnerability definitions (CVE data, descriptions, CVSS scores) |
| `sn_vul_group` | Vulnerability Groups — groups of related Vulnerable Items |
| `sn_vul_third_party_entry` | Third-party scanner vulnerability data (before reconciliation) |
| `sn_vul_solution` | Remediation solutions (patches, config changes) |
| `sn_vul_task` | Remediation Tasks linked to Vulnerable Items |

---

### Scanner Integration

VR integrates with vulnerability scanners via **Integration Hub spokes** or **Import Sets**.

#### Qualys Integration
```
Navigator: Vulnerability Response → Integrations → Qualys
Requirements:
- Qualys API credentials configured in ServiceNow Credentials store
- Qualys spoke installed from ServiceNow Store
- Schedule data import job (Navigator: VR → Qualys → Import Schedule)

Data flow: Qualys API → ServiceNow import → sn_vul_third_party_entry → reconcile → sn_vul_vulnerable_item
```

#### Tenable Integration
```
Navigator: Vulnerability Response → Integrations → Tenable
- Supports Tenable.io and Tenable.sc (SecurityCenter)
- Uses Tenable spoke or CSV/XML import via Import Sets
- Configure: credential, scan policies, import schedule
```

#### Rapid7 InsightVM Integration
```
Navigator: Vulnerability Response → Integrations → Rapid7
- Uses Rapid7 API or InsightVM connector
- Imports scan results into Vulnerable Items
```

#### NVD (National Vulnerability Database) Sync
```
Navigator: Vulnerability Response → NVD Integration
- Pulls CVE data from NVD to enrich sn_vul_entry records
- Adds CVSS v3 scores, CWE classifications, description
- Runs on schedule (default: daily)
- Source: https://nvd.nist.gov
```

> Source: [ServiceNow Docs — VR integrations](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/concept/c_VulnerabilityResponseIntegrations.html)

---

### Vulnerable Items

A **Vulnerable Item** (`sn_vul_vulnerable_item`) is the core record in VR — it represents **one vulnerability on one CI**.

**Key fields:**

| Field | Description |
|---|---|
| CI (Configuration Item) | The affected asset from the CMDB |
| Vulnerability | The CVE or finding (from `sn_vul_entry`) |
| State | Open → In Progress → Review → Closed / Exception |
| Risk score | Calculated from CVSS + asset criticality |
| Assignment group | Team responsible for remediation |
| SLA | Time remaining to remediate |
| Last found date | When the scanner last detected this finding |
| Fixed date | When remediation was confirmed |

**State transitions:**
```
Open → In Progress (remediation started)
     → Exception (accepted risk / false positive)
     → Closed — Fixed (scanner no longer detects it)
     → Closed — Duplicate
```

---

### Vulnerability Groups

**Vulnerability Groups** aggregate related Vulnerable Items so remediation can be managed collectively — e.g., all instances of CVE-2021-44228 (Log4Shell) across the environment in one group.

```
Navigator: Vulnerability Response → Vulnerability Groups
```

**Grouping strategies:**
- **By CVE** — all items with the same CVE in one group (most common)
- **By CI group** — all vulnerabilities on a specific server group
- **By scan source** — all items from one scanner
- **Manual** — custom groups for specific campaigns

**Group remediation:**
```
On a Vulnerability Group → click Create Remediation Task / Create Change Request
→ One remediation record manages all items in the group
→ Items automatically closed when remediation is verified
```

---

### Remediation Workflows

#### Remediation Tasks

Lightweight task records (`sn_vul_task`) used for straightforward patches — assigned directly to a team.

```
Navigator: VR → Remediation Tasks
Or: From a Vulnerable Item / Group → Create Remediation Task
```

#### Change Requests

For changes that require formal change management (CAB approval, scheduled maintenance windows):

```
From a Vulnerability Group → Create Change Request
→ Standard ITSM Change Request created (change_request table)
→ Linked to the Vulnerability Group
→ When Change Request closes as Successful → Vulnerable Items automatically close
```

#### SLA Management

```
Navigator: VR → Administration → SLA Definitions
Standard SLA targets (customise to your org's policy):
- Critical (CVSS ≥ 9.0): 30 days
- High (CVSS 7.0–8.9): 60 days
- Medium (CVSS 4.0–6.9): 90 days
- Low (CVSS < 4.0): 180 days

SLA breach: item flagged as Overdue; escalation rules fire
```

---

### Exception Handling

Not every vulnerability can be remediated immediately. VR supports **Exceptions** for:
- **Accepted Risk** — risk acknowledged and accepted by a business owner
- **False Positive** — scanner incorrectly flagged a non-existent vulnerability
- **Compensating Control** — mitigating control in place makes remediation unnecessary

```
On a Vulnerable Item → Exception tab → Request Exception
Fields: Justification, Expiry Date, Approver
Exception requires approval → approval workflow fires
Approved → item moves to Exception state (not counted as open)
```

> Source: [ServiceNow Docs — VR exceptions](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/task/t_CreateVulnerabilityException.html)

---

### CMDB Integration

VR depends on the CMDB for CI context. Key considerations:

- Vulnerable Items are linked to `cmdb_ci` records — CI must exist in CMDB for VR to import cleanly
- **CI criticality** in the CMDB affects the **risk score** of Vulnerable Items on that CI — a Critical CI with a Medium CVSS score may have a higher risk score than a Non-Critical CI with High CVSS
- Scanner hostnames must **match or be reconciled** to CMDB CI names — mismatches leave vulnerabilities unlinked
- Configure: VR → Administration → CI Identification → matching rules

---

### Configuration Compliance (related module)

ServiceNow **Configuration Compliance** tests whether CI configurations meet security policies (e.g., CIS Benchmarks). It works alongside VR but is a separate module.

```
Navigator: Configuration Compliance → Test Results
- Results tied to CIs in CMDB
- Failures generate Compliance Findings (similar to Vulnerable Items)
- Can be remediated via Change Requests
```

---

### VR Roles

| Role | Access |
|---|---|
| `sn_vul.admin` | Full VR administration |
| `sn_vul.analyst` | Create, update, close Vulnerable Items and Groups |
| `sn_vul.reader` | Read-only access |
| `sn_vul.manager` | Manage assignments and queues |

> Source: [ServiceNow Docs — VR roles](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/reference/r_VRRoles.html)

---

### VR Key Properties

```
Navigator: Vulnerability Response → Administration → Properties

Key properties:
- sn_vul.auto_close_fixed_items       → auto-close items when scanner no longer detects them
- sn_vul.default_assignment_group     → default remediation team
- sn_vul.risk_score_formula           → how risk score is calculated (CVSS × asset criticality)
- sn_vul.max_items_per_group          → cap on Vulnerable Items per group
```

---

### Reporting & Metrics

```
Pre-built VR dashboards:
- Open Vulnerabilities by Severity
- Vulnerability Age (how long items have been open)
- SLA Compliance rate
- Remediation velocity (items closed per week)
- Top CVEs by affected CI count
- Exposure by business service

Navigator: Vulnerability Response → Dashboard (or Reports)
```

**Mean Time to Remediate (MTTR):** key metric tracked in VR — from `sn_vul_vulnerable_item.first_found` to `sn_vul_vulnerable_item.fixed_date`.

---

## Common Exam Scenarios

**Scenario 1:** A Qualys scan found CVE-2021-44228 on 150 servers. How do you manage this efficiently rather than 150 individual remediation records?
→ Create a **Vulnerability Group** for CVE-2021-44228 → create one Change Request on the group → all 150 items close when the Change is complete.

**Scenario 2:** A scanner imports a Vulnerable Item but it cannot be linked to a CI in the CMDB. What is the most likely cause?
→ The scanner hostname does not match any CI in the CMDB. Configure **CI Identification matching rules** in VR Administration to reconcile scanner hostnames to CMDB CIs.

**Scenario 3:** A business owner accepts the risk for a vulnerability that cannot be patched immediately. How is this recorded in VR?
→ Create an **Exception** on the Vulnerable Item with type "Accepted Risk", expiry date, and justification. Submit for approval.

**Scenario 4:** Vulnerable Items are not auto-closing after the scanner confirms the fix. What property should you check?
→ `sn_vul.auto_close_fixed_items` — ensure it is set to `true`.

**Scenario 5:** The risk score for a Critical server's Medium CVSS vulnerability is higher than a Non-Critical server's High CVSS vulnerability. Why?
→ VR risk score factors in **CI criticality from the CMDB** — a critical business system inflates the risk score beyond the raw CVSS value.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — VR path** | https://nowlearning.servicenow.com — Security Operations Fundamentals + VR Implementation |
| **ServiceNow Docs — VR** | https://docs.servicenow.com — search "Vulnerability Response" |
| **NVD (CVE data source)** | https://nvd.nist.gov — CVSS scores, CVE definitions used by VR |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CIS-SIR — Security Incident Response (companion SecOps application)
- ServiceNow-CSA — Platform fundamentals, CMDB
- Vulnerability-Management — Vulnerability management methodology (NIST)
- ServiceNow-CIS-DF — CMDB fundamentals (VR depends heavily on CMDB)

---

## References

- ServiceNow Docs — Vulnerability Response: https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/concept/vulnerability-response.html
- ServiceNow Docs — VR Roles: https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/reference/r_VRRoles.html
- ServiceNow Docs — VR Exceptions: https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/task/t_CreateVulnerabilityException.html
- ServiceNow Docs — VR Integrations: https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/vulnerability-response/concept/c_VulnerabilityResponseIntegrations.html
- NVD (CVSS source): https://nvd.nist.gov
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]