---
title: "ServiceNow CIS-TPRM — Certified Implementation Specialist: Third Party Risk Management"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-TPRM, GRC, third-party, vendor-risk, study-guide, job-aid, intermediate]
source: "ServiceNow official TPRM documentation (docs.servicenow.com), Now Learning CIS-TPRM training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-TPRM — Certified Implementation Specialist: Third Party Risk Management

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Third Party Risk Management |
| Abbreviation | CIS-TPRM |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | GRC: Third Party Risk Management (Now Learning) |
| Recommended experience | ServiceNow administration; familiarity with vendor/supplier risk management |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official TPRM docs:** https://docs.servicenow.com/bundle/washingtondc-governance-risk-compliance/page/product/third-party-risk-management/concept/c_ThirdPartyRiskMgmt.html

---

## What CIS-TPRM Validates

CIS-TPRM certifies you can **implement and configure ServiceNow Third Party Risk Management** — enabling organisations to assess, monitor, and manage the risk posed by vendors, suppliers, and other third parties.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| TPRM Architecture | Data model, third party profiles, contact management |
| Vendor Lifecycle | Onboarding, tiering, periodic assessments, offboarding |
| Assessments & Questionnaires | Assessment templates, questionnaire designer, scoring |
| Issues & Remediation | Issue management, remediation tasks, exceptions |
| Monitoring & Continuous Assessments | Scheduled reassessments, tier-based frequency |
| Integration | GRC integration, ITSM, CMDB relationships |
| Roles & Permissions | TPRM roles, vendor portal access |

---

## Core Concepts — Study & Job Aid

### TPRM Data Model

```
Third Party (vendor/supplier record)
  └── Third Party Contact (vendor POC)
  └── Third Party Profile (specific engagement/product/service)
        └── Assessment (risk questionnaire sent to vendor)
              └── Assessment Response (vendor answers)
                    └── Risk Score + Issues
```

**Key tables:**
| Table | Purpose |
|---|---|
| `sn_tprm_party` | Third Party (vendor) records |
| `sn_tprm_party_profile` | Third Party Profile records |
| `sn_tprm_assessment` | Assessment records |
| `sn_tprm_assessment_task` | Assessment questionnaire tasks |

---

### Third Party Lifecycle

**Navigator:** Third Party Risk Management → Third Parties

**Onboarding workflow:**
```
Third Party created → Profile created → Tier assigned
→ Initial Assessment sent → Responses collected
→ Risk Score calculated → Issues identified
→ Remediation tracked → Approved for use (or rejected)
```

**Vendor tiering:**
```
Tier 1 — Critical: highest risk, most data access, annual+ assessments
Tier 2 — High: significant risk, periodic assessments
Tier 3 — Medium: moderate risk, less frequent assessments
Tier 4 — Low: minimal risk, self-attestation or light assessment
```

Tier determines assessment **frequency and depth**.

---

### Assessments and Questionnaires

**Assessment types:**
| Type | Purpose |
|---|---|
| Initial Assessment | First-time onboarding risk evaluation |
| Periodic Assessment | Scheduled recurring reassessment |
| Event-Driven Assessment | Triggered by incident, breach, or change |
| Focused Assessment | Targeted review of specific risk area |

**Creating an Assessment Template:**
```
Navigator: Third Party Risk Management → Administration → Assessment Templates → New
Fields: Name, Assessment Type, Questionnaire, Scoring criteria
Sections: Group questions by domain (Security, Privacy, Business Continuity)
```

**Questionnaire Designer:**
```
Navigator: Assessment Designer → Templates
Build questions with: multiple choice, open text, yes/no, file upload
Set weights per question → drives overall risk score
```

**Scoring:**
```
Each question has a weight
Vendor response maps to a score (e.g. "Yes" = 5, "Partial" = 3, "No" = 0)
Total score → Risk Tier assignment (Low / Medium / High / Critical)
```

---

### Issues and Remediation

**Issue generation:**
- Issues auto-generated when assessment responses fall below threshold
- Issues linked to the Third Party Profile
- Navigator: Third Party Risk Management → Issues

**Remediation workflow:**
```
Issue created → Vendor notified (or internal owner)
→ Remediation Plan submitted by vendor
→ Evidence reviewed by risk team
→ Issue verified and closed (or exception created)
```

---

### Continuous Monitoring

**Tier-based reassessment schedules:**
```
Tier 1: every 6–12 months (configurable)
Tier 2: every 12 months
Tier 3: every 18–24 months
Tier 4: as needed / self-attestation
```

**Scheduled assessment automation:**
```
Navigator: Third Party Risk Management → Administration → Assessment Schedules
Define: Tier, Frequency, Assessment Template, Notification lead time
System auto-creates assessments per schedule
```

**External data feeds (optional):**
- BitSight, SecurityScorecard integration for continuous external scoring
- Scores visible on Third Party Profile → trigger event-driven assessments if score drops

---

### Vendor Portal

- Third party contacts can respond to assessments via the **Service Portal**
- No ServiceNow licence required for vendor portal users
- Portal access configured via: System Properties → TPRM Portal settings
- Vendor receives email with portal link → logs in → completes questionnaire

---

### TPRM Roles

| Role | Access |
|---|---|
| `sn_tprm.user` | View TPRM records |
| `sn_tprm.manager` | Manage third parties, assessments, issues |
| `sn_tprm.admin` | Full TPRM configuration and admin |
| `sn_tprm.vendor` | Vendor portal access (external, limited) |

---

## Common Exam Scenarios

**Scenario 1:** A critical vendor has not responded to their annual assessment. What should you do in ServiceNow?
→ Review the **Assessment** record → send a reminder notification → if still no response, escalate via the Issue creation workflow and flag the Third Party Profile as non-compliant.

**Scenario 2:** The risk team wants to automatically reassess Tier 1 vendors every 6 months. How do you configure this?
→ Create an **Assessment Schedule** tied to Tier 1 → set frequency to 6 months → attach the relevant Assessment Template.

**Scenario 3:** A vendor's BitSight score dropped below 600. The team wants this to trigger an automatic assessment. What is required?
→ Configure an **event-driven assessment rule** — when the external score falls below the threshold, auto-create a Focused Assessment for that vendor.

**Scenario 4:** A vendor cannot remediate a finding within 30 days due to a third-party dependency. What record do you create?
→ An **Exception** — documenting the business justification, expected remediation date, and approver acceptance of residual risk.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — TPRM path** | https://nowlearning.servicenow.com — GRC: Third Party Risk Management |
| **ServiceNow Docs — TPRM** | https://docs.servicenow.com — search "Third Party Risk Management" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CIS-RC — GRC Risk and Compliance (closely related)
- ServiceNow-CSA — Platform fundamentals

---

## References

- ServiceNow Docs — Third Party Risk Management: https://docs.servicenow.com/bundle/washingtondc-governance-risk-compliance/page/product/third-party-risk-management/concept/c_ThirdPartyRiskMgmt.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]