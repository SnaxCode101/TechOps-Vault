---
title: "ServiceNow CIS-RC — Certified Implementation Specialist: Risk and Compliance"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-RC, GRC, risk, compliance, audit, study-guide, job-aid, intermediate]
source: "ServiceNow official GRC documentation (docs.servicenow.com), Now Learning CIS-RC training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-RC — Certified Implementation Specialist: Risk and Compliance

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Risk and Compliance |
| Abbreviation | CIS-RC |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | GRC: Risk Management + GRC: Policy and Compliance (Now Learning) |
| Recommended experience | ServiceNow administration; familiarity with GRC concepts (risk, compliance, audit) |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official GRC docs:** https://docs.servicenow.com/bundle/washingtondc-governance-risk-compliance/page/product/grc/concept/c_GRC.html

---

## What CIS-RC Validates

CIS-RC certifies you can **implement and configure ServiceNow GRC** — Governance, Risk, and Compliance — covering Policy and Compliance Management, Risk Management, and Audit Management modules.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| GRC Architecture | Scoped vs global app, GRC data model, entity/content packs |
| Policy & Compliance | Policies, controls, citations, attestations, control tests |
| Risk Management | Risk framework, risk register, risk assessment, risk response |
| Audit Management | Audit engagements, audit tasks, evidence collection |
| Issues & Remediation | Issue creation, remediation tasks, exception handling |
| Integration | CMDB integration, ITSM relationship, VR integration |
| Roles & Access | GRC roles, scoped app permissions |

---

## Core Concepts — Study & Job Aid

### GRC Data Model

```
Authority Document (regulation, standard, policy)
  └── Citation (specific clause/requirement)
        └── Policy (internal policy derived from citations)
              └── Control (how you meet the policy)
                    └── Control Test (how you verify the control)
                          └── Test Result (pass/fail evidence)
```

**Key tables:**
| Table | Purpose |
|---|---|
| `sn_compliance_policy` | Policy records |
| `sn_compliance_control` | Control records |
| `sn_risk_risk` | Risk records |
| `sn_audit_engagement` | Audit engagement records |
| `sn_grc_profile` | Entity profiles (CI, business entity) |

---

### Policy and Compliance Management

**Navigator:** GRC → Policy and Compliance → Policies

**Policy lifecycle:**
```
Draft → Review → Published → Retired
```

**Control attestation:**
```
Published Control → attestation scheduled → control owner completes attestation
→ Evidence uploaded → Test Result created (Satisfactory / Not Satisfactory)
```

**Citations:**
- Link external authority document requirements (e.g. ISO 27001 clause 6.1) to internal policies
- Navigator: GRC → Policy and Compliance → Authority Documents → Citations

**Content Library (SN Content Packs):**
- Pre-built authority documents for NIST, ISO, GDPR, HIPAA, SOX
- Installed from ServiceNow Store → reduces manual authority document build

---

### Risk Management

**Navigator:** GRC → Risk Management → Risks

**Risk record fields:**

| Field | Purpose |
|---|---|
| Risk Category | Type of risk (Operational, Compliance, Financial) |
| Likelihood | Probability of occurrence |
| Impact | Business impact if realised |
| Inherent Risk Score | Before controls applied |
| Residual Risk Score | After controls applied |
| Risk Response | Accept / Mitigate / Transfer / Avoid |
| Risk Owner | Accountable person |
| Related Controls | Controls that mitigate this risk |

**Risk scoring matrix:**
```
Score = Likelihood × Impact
Likelihood: 1 (Rare) → 5 (Almost Certain)
Impact:     1 (Insignificant) → 5 (Catastrophic)
Range:      1 (lowest) → 25 (critical)
```

**Risk Assessment process:**
```
Risk Entity Profile assigned → Risk Assessment created
→ Risk questions answered by risk owner
→ Risk scores calculated
→ Review and approval workflow
```

---

### Audit Management

**Navigator:** GRC → Audit Management → Engagements

**Audit Engagement lifecycle:**
```
Planning → Scoping → Fieldwork → Reporting → Closure
```

**Evidence collection:**
- Audit tasks assigned to control owners
- Evidence attached (documents, screenshots, test results)
- Navigator: Audit Engagement → Audit Tasks → Evidence

**Findings and Issues:**
```
Audit Finding → Issue created → Remediation Task assigned
→ Remediation completed → Issue verified and closed
```

---

### Issues and Remediation

**Issue types:**
| Type | Source |
|---|---|
| Compliance Issue | Control test failure |
| Risk Issue | Risk response action item |
| Audit Issue | Audit finding |

**Remediation workflow:**
```
Issue created → Risk/Compliance owner notified
→ Remediation Task created with due date
→ Owner completes task + attaches evidence
→ Issue reviewer verifies and closes
```

**Exception handling:**
```
If remediation is not feasible → create Exception
Exception fields: justification, expiry date, approver
Exception approved → Issue moved to Exceptions state
```

---

### GRC Roles

| Role | Access |
|---|---|
| `sn_compliance.user` | View compliance records |
| `sn_compliance.manager` | Manage policies and controls |
| `sn_risk.user` | View risk records |
| `sn_risk.manager` | Manage risk register and assessments |
| `sn_audit.user` | View audit records |
| `sn_audit.manager` | Manage audit engagements |
| `sn_grc.admin` | Full GRC admin access |

---

## Common Exam Scenarios

**Scenario 1:** A control test fails but the business unit cannot remediate within the required timeframe. What should you create?
→ An **Exception** record — documents the justification, expiry date, and approver acceptance of residual risk.

**Scenario 2:** Your organisation needs to map ISO 27001 clause 6.1.2 to internal controls. What is the correct GRC object?
→ A **Citation** from an Authority Document representing ISO 27001, linked to the relevant Policy.

**Scenario 3:** A Risk owner says the Residual Risk score is higher than expected despite several controls being in place. What should you check?
→ Verify the **Control Test Results** — if controls have not passed attestation, they may not be reducing the residual risk score.

**Scenario 4:** The audit team needs to collect evidence from asset owners as part of a compliance audit. What GRC object facilitates this?
→ **Audit Tasks** within an Audit Engagement — assigned to asset owners to collect and attach evidence.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — GRC path** | https://nowlearning.servicenow.com — GRC: Risk Management + GRC: Policy and Compliance |
| **ServiceNow Docs — GRC** | https://docs.servicenow.com — search "GRC", "Risk Management", "Policy and Compliance" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-VR — Vulnerability Response (related risk/security context)
- ServiceNow-CIS-ITSM — ITSM integration with GRC issues

---

## References

- ServiceNow Docs — GRC: https://docs.servicenow.com/bundle/washingtondc-governance-risk-compliance/page/product/grc/concept/c_GRC.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]