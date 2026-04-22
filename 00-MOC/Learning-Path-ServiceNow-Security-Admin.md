---
title: Learning Path — ServiceNow Security Admin
tags:
  - MOC
  - learning-path
  - ServiceNow
  - security-admin
  - foundational
  - intermediate
topic: Learning Paths
difficulty: foundational
created: 2026-04-03
updated: 2026-04-03
source: SecOps Vault — Session 9
---

# Learning Path — ServiceNow Security Admin

## Overview
This path builds the knowledge required to administer ServiceNow with a security focus — from platform fundamentals through security-specific modules (SIR, VR, TPRM) and certification progression. Estimated total time: **80–120 hours**.

Target roles: ServiceNow Administrator, ServiceNow Security Admin, GRC Admin, IT Security Analyst (ServiceNow shop), Platform Engineer.

---

## Phase 1 — Platform Foundations (Est. 20–25 hrs)

Learn the ServiceNow platform before touching any security modules. You cannot configure security workflows without understanding how the platform operates.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 1 | [[ServiceNow-Micro-Cert-Welcome]] | Foundational | 1 hr | None |
| 2 | [[ServiceNow-CSA]] | Foundational | 8 hrs | Welcome micro-cert |
| 3 | [[ServiceNow-Navigator-Shortcuts]] | Foundational | 1 hr | CSA in progress |
| 4 | [[ServiceNow-Micro-Cert-Flows]] | Foundational | 2 hrs | CSA in progress |
| 5 | [[ServiceNow-Flow-Designer]] | Intermediate | 2 hrs | Micro-Cert-Flows |
| 6 | [[ServiceNow-Micro-Cert-Platform-Analytics]] | Foundational | 2 hrs | CSA in progress |
| 7 | [[ServiceNow-Micro-Cert-ATF]] | Foundational | 2 hrs | CSA in progress |
| 8 | [[ServiceNow-JavaScript]] | Intermediate | 3 hrs | CSA complete |

> [!important] CSA First
> The ==ServiceNow CSA (Certified System Administrator)== is the required foundation for every other ServiceNow certification. Do not skip to advanced modules before earning or studying thoroughly for the CSA.
> Current status: 🎯 In Progress — see [[ServiceNow-CSA]]

---

## Phase 2 — Development and Automation (Est. 15–20 hrs)

Understand how to build and customize ServiceNow — essential for configuring security workflows.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 9 | [[ServiceNow-CAD]] | Intermediate | 6 hrs | CSA complete |
| 10 | [[ServiceNow-CTA]] | Advanced | 6 hrs | CAD complete |
| 11 | [[ServiceNow-CMA]] | Intermediate | 4 hrs | CSA complete |

> [!tip] Phase 2 Checkpoint
> You should be able to: create and modify tables, build flows in Flow Designer, write basic GlideScript, configure ACLs, and use the ATF to test platform changes.

---

## Phase 3 — ITSM and Core Modules (Est. 10–15 hrs)

Security in ServiceNow depends on ITSM fundamentals — incidents, changes, and problems feed directly into security workflows.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 12 | [[ServiceNow-CIS-ITSM]] | Intermediate | 4 hrs | CSA complete |
| 13 | [[ServiceNow-CIS-SM]] | Intermediate | 3 hrs | CIS-ITSM |
| 14 | [[ServiceNow-CIS-RC]] | Intermediate | 3 hrs | CIS-ITSM |

---

## Phase 4 — Security-Specific Modules (Est. 20–25 hrs)

This is the security-focused core of the path. These modules cover incident response, vulnerability response, and third-party risk directly within ServiceNow.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 15 | [[ServiceNow-CIS-SIR]] | Intermediate | 5 hrs | CIS-ITSM complete |
| 16 | [[ServiceNow-CIS-VR]] | Intermediate | 4 hrs | CIS-SIR |
| 17 | [[ServiceNow-CIS-TPRM]] | Advanced | 5 hrs | CIS-VR |
| 18 | [[ServiceNow-CIS-PA]] | Intermediate | 3 hrs | CIS-ITSM |
| 19 | [[ServiceNow-CIS-DF]] | Intermediate | 3 hrs | CIS-ITSM |

> [!important] Security Module Focus Areas
> - ==SIR (Security Incident Response):== Manage security incidents end-to-end within ServiceNow; integrates with SIEM tools
> - ==VR (Vulnerability Response):== Ingest scanner data (Tenable, Qualys); track remediation; SLA-driven workflows
> - ==TPRM (Third-Party Risk Management):== Assess and monitor vendor security posture; questionnaires and scoring
> - ==PA (Performance Analytics):== Build security dashboards and KPI tracking; critical for reporting to leadership

---

## Phase 5 — Extended Platform Modules (Est. 10–15 hrs)

Broaden platform knowledge for cross-functional security admin work.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 20 | [[ServiceNow-CIS-HAM]] | Intermediate | 3 hrs | CSA complete |
| 21 | [[ServiceNow-CIS-SAM]] | Intermediate | 3 hrs | CIS-HAM |
| 22 | [[ServiceNow-CIS-CSM]] | Intermediate | 3 hrs | CIS-ITSM |
| 23 | [[ServiceNow-CIS-SPM]] | Intermediate | 3 hrs | CIS-ITSM |

---

## Certification Progression

| Order | Cert | Vault Note | Status |
|-------|------|-----------|--------|
| 1 | CSA | [[ServiceNow-CSA]] | 🎯 In Progress |
| 2 | Micro-Cert: Welcome | [[ServiceNow-Micro-Cert-Welcome]] | ✅ Earned 2026-03-25 |
| 3 | Micro-Cert: Flows | [[ServiceNow-Micro-Cert-Flows]] | ✅ Earned 2026-03-26 |
| 4 | Micro-Cert: Platform Analytics | [[ServiceNow-Micro-Cert-Platform-Analytics]] | ✅ Earned 2026-03-26 |
| 5 | Micro-Cert: ATF | [[ServiceNow-Micro-Cert-ATF]] | ✅ Earned 2026-03-27 |
| 6 | CAD | [[ServiceNow-CAD]] | 📚 After CSA |
| 7 | CIS-ITSM | [[ServiceNow-CIS-ITSM]] | 📚 After CAD |
| 8 | CIS-SIR | [[ServiceNow-CIS-SIR]] | 📚 After CIS-ITSM |
| 9 | CIS-VR | [[ServiceNow-CIS-VR]] | 📚 After CIS-SIR |
| 10 | CTA | [[ServiceNow-CTA]] | 📚 Advanced |

---

## Integration with Security Knowledge

The following vault notes are directly relevant to ServiceNow security admin work:

| Note | Why It Matters |
|------|---------------|
| [[Vulnerability-Management]] | Core concept behind VR module workflows |
| [[Incident-Response]] | Core concept behind SIR module workflows |
| [[Threat-Intelligence]] | Feeds into SIR detection and triage |
| [[System-Admin-Reference]] | Underlying IT admin skills |
| [[Python-for-Security]] | Scripting ServiceNow integrations via REST API |

---

## See Also
- [[Learning-Path-SOC-Analyst]]
- [[Learning-Path-Healthcare-IT-Epic-Security]]
- [[TechOps-MOC]]
- [[Dashboard]]
