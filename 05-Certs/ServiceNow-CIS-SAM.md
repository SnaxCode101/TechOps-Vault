---
title: "ServiceNow CIS-SAM — Certified Implementation Specialist: Software Asset Management"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-SAM, software-asset-management, licensing, compliance, study-guide, job-aid, intermediate]
source: "ServiceNow official SAM documentation (docs.servicenow.com), Now Learning CIS-SAM training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-SAM — Certified Implementation Specialist: Software Asset Management

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Software Asset Management |
| Abbreviation | CIS-SAM |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | SAM Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; software licensing and compliance concepts |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official SAM docs:** https://docs.servicenow.com/bundle/washingtondc-it-asset-management/page/product/software-asset-management/concept/c_SoftwareAssetMgmt.html

---

## What CIS-SAM Validates

CIS-SAM certifies you can **implement and configure ServiceNow Software Asset Management** — tracking software installations, reconciling against licence entitlements, identifying compliance gaps, and managing the software asset lifecycle.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| SAM Architecture | Software models, entitlements, installations, reconciliation |
| Software Discovery Integration | Discovery/SCCM data feeding SAM |
| Entitlement Management | Licence types, entitlement records, allocation |
| Software Reconciliation | Compliance position, over/under-licensed |
| Software Normalisation | Publisher normalisation, model normalisation |
| Reclamation | Harvesting unused licences |
| Software Spend Optimisation | Cost savings, reclamation workflows |

---

## Core Concepts — Study & Job Aid

### SAM Data Model

```
Publisher (e.g. Microsoft, Adobe)
    └── Software Model (e.g. Microsoft Office 365 Business)
          ├── Software Installations (discovered installs on CIs)
          └── Licence Entitlement (purchased licences)
                └── Reconciliation → Compliance Position
```

**Key tables:**
| Table | Purpose |
|---|---|
| `cmdb_sam_sw_publisher` | Publisher records |
| `cmdb_model` (type=Software) | Software model records |
| `cmdb_software_instance` | Discovered software installations |
| `samp_sw_entitlement` | Licence entitlement records |
| `samp_reconciliation_result` | Reconciliation output |

---

### Software Installations

Software installations are populated by:
- **ServiceNow Discovery** — reads installed software via WMI (Windows), package managers (Linux)
- **SCCM/MECM import** — import from Microsoft Configuration Manager
- **Manual import** — CSV upload for legacy systems

```
Navigator: Software Asset Management → Software Installations
```

Each installation record links to:
- A **Software Model** (what software)
- A **CI** (which computer it's installed on)
- A **User** (who uses it, if known)

---

### Entitlements

An **Entitlement** represents the purchased right to use software.

```
Navigator: Software Asset Management → Entitlements → New
Fields:
  Software Model:     Microsoft Office 365 Business
  Entitlement type:   Named User / Device / Concurrent / Site licence
  Quantity:           500
  Purchase order:     link to PO
  Start / End date:   licence term
  Cost per unit:      £25/month
```

**Licence types:**
| Type | Meaning |
|---|---|
| Named User | One named person per licence (most common) |
| Device | One device per licence (not tied to user) |
| Concurrent | Max simultaneous users (need usage monitoring) |
| Site Licence | Unlimited use within a defined site/org |
| OEM | Bundled with hardware — not transferrable |
| Subscription | Time-limited, must renew or remove |

---

### Software Normalisation

Raw discovery data contains inconsistent software names:
```
"Microsoft Office 365", "MS Office365", "Office 365 Business"
→ All normalised to → Software Model: "Microsoft Office 365 Business"
```

**Publisher Normalisation:**
```
Navigator: Software Asset Management → Administration → Publisher Normalization
Maps: "MSFT", "Microsoft Corp" → "Microsoft"
```

**Model Normalisation:**
```
Navigator: Software Asset Management → Administration → Model Normalization
Maps variant install names → canonical Software Model
```

Normalisation is **critical** — without it, reconciliation produces inaccurate compliance results.

---

### Reconciliation

Reconciliation compares **what you're using** (installations) vs **what you've bought** (entitlements).

```
Navigator: Software Asset Management → Reconciliation → Run Reconciliation
```

**Reconciliation result:**
| State | Meaning |
|---|---|
| Compliant | Installations ≤ Entitlements (licensed correctly) |
| Over-licensed | Installations < Entitlements (paying for unused licences) |
| Under-licensed | Installations > Entitlements (compliance risk — audit exposure) |

**Compliance position visible at:**
```
Navigator: Software Asset Management → Compliance → Compliance Overview
```

---

### Reclamation (Harvesting Unused Licences)

Reclamation identifies and recovers licences assigned to users who no longer need them.

**Reclamation triggers:**
- Software not launched in X days (usage metering required)
- User left the organisation
- Device decommissioned

```
Navigator: Software Asset Management → Reclamation → Reclamation Candidates
```

**Reclamation workflow:**
```
Candidate identified → Notification sent to user/manager
→ If no response within X days → Software uninstalled or entitlement revoked
→ Licence returned to pool → Compliance position improves
```

**Usage metering:**
- Requires metering agents (SCCM, Flexera) or ServiceNow Metering
- Records last-used date per installation
- Drives reclamation candidates

---

### SAM Roles

| Role | Access |
|---|---|
| `sam_user` | View SAM records |
| `sam_analyst` | Manage entitlements, run reconciliation |
| `sam_manager` | Full SAM management including reclamation |
| `itam_admin` | Full ITAM/SAM administration |

---

## Common Exam Scenarios

**Scenario 1:** Reconciliation shows 200 installations of Adobe Acrobat but only 150 entitlements. What is the compliance position and what should you do?
→ **Under-licensed** — 50 installations exceed entitlements, creating audit exposure. Either purchase 50 additional licences or identify and reclaim 50 installations.

**Scenario 2:** Raw discovery data shows "Microsoft Corp", "MSFT", and "Microsoft" as publishers for the same vendor. How do you fix this?
→ Run **Publisher Normalisation** — map all variants to the canonical publisher name "Microsoft" to ensure correct reconciliation.

**Scenario 3:** The team wants to recover licences from users who haven't opened the software in 90 days. What SAM feature supports this?
→ **Reclamation** with **usage metering** — configure the reclamation threshold to 90 days, identify candidates, and trigger the reclamation workflow.

**Scenario 4:** The legal team wants to verify whether all SQL Server installations are covered by valid entitlements before a scheduled Microsoft audit. What do you run?
→ A **Reconciliation** scoped to SQL Server → review the compliance position to identify any under-licensed installations before the audit.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — SAM path** | https://nowlearning.servicenow.com — SAM Fundamentals |
| **ServiceNow Docs — SAM** | https://docs.servicenow.com — search "Software Asset Management" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-HAM — Hardware Asset Management (sister certification)
- ServiceNow-CIS-Disco — Discovery populates software installation data

---

## References

- ServiceNow Docs — SAM: https://docs.servicenow.com/bundle/washingtondc-it-asset-management/page/product/software-asset-management/concept/c_SoftwareAssetMgmt.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]