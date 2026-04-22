---
title: Epic EHR — Section Overview & MOC
type: moc
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - ehr
  - mychart
  - moc
  - overview
  - healthcare-it
  - intermediate
source: Epic UserWeb (userWeb.epic.com), Epic training materials, Epic Systems
  Corporation documentation
difficulty: intermediate
---

# 🏥 Epic EHR — Section Overview

> This section covers Epic EHR system administration, MyChart configuration, scheduling, reporting, and clinical workflow knowledge required for Epic application analyst / support roles.

> ⚠️ **Source Note:** Epic documentation is proprietary and gated behind Epic UserWeb credentials. All notes in this section are built from Epic UserWeb training materials, Epic community knowledge, and publicly available Epic resources. Flag: any item marked `[VERIFY: Epic UserWeb]` should be confirmed against your licensed Epic training environment before use in production.

---

## 🗺️ Section Notes

| Note | Topic |
|---|---|
| [[Epic-MyChart-Rules]] | MyChart access rules, patient matching, security configuration |
| [[Epic-Session-Definitions-Limits]] | Session definitions, timeout settings, concurrent session limits |
| [[Epic-Reporting-Workbench-Slicer-Dicer]] | Reporting Workbench reports, Slicer Dicer cohort building |
| [[Epic-Content-Management]] | Content management, preference lists, order sets, navigator sections |
| [[Epic-Department-Build-Facility-Structure]] | Department build, facility/location hierarchy, bed management |
| [[Epic-Patient-Self-Service]] | Patient self-service, eCheck-in, online scheduling, questionnaires |
| [[Epic-Pools-Blocks-Modifiers]] | Scheduling pools, template blocks, visit type modifiers |
| [[Epic-Questionnaire-Build]] | Questionnaire build, HQMFs, patient-facing forms |
| [[Epic-Provider-Masterfile]] | Provider masterfile, SER record build, add/remove settings |
| [[Epic-SmartText-SmartPhrases]] | SmartTexts, SmartPhrases, SmartLists, SmartLinks |
| [[Epic-Clinic-Workflow]] | Primary & specialty clinic workflows, scheduling, rooming, documentation |

---

## 🏗️ Epic Architecture Quick Reference

```
Epic Environment Hierarchy
━━━━━━━━━━━━━━━━━━━━━━━━━
Production (PRD)
  └── Reporting (RPT) — read-only mirror
  └── Disaster Recovery (DR)

Non-Production
  └── TEST (TST) — change testing
  └── TRAIN (TRN) — end-user training
  └── PLAYGROUND (PLY) / POC — sandboxing
```

### Key Epic Databases / Chronicles
- **Chronicles** — Epic's proprietary INI-based database (not SQL)
- **Clarity** — SQL Server read replica for reporting
- **Caboodle** — Analytical data warehouse (EDW)
- **Hyperspace** — Clinical workstation client application
- **MyChart** — Patient-facing web/mobile portal

### Master Files (INI Records)
Every Epic record is a "master file" (MF) record. Key MFs:
| MF | Abbreviation | Record Type |
|---|---|---|
| Patient | EPT | Patient demographics |
| Provider | SER | Clinician / staff records |
| Department | DEP | Scheduling / ADT department |
| Location | LOC | Physical location |
| Visit Type | EAP | Appointment types |
| User | EMP | System user accounts |
| Template | SCH | Schedule templates |
| Questionnaire | QNS | Patient questionnaires |

---

## ⚡ Quick-Access by Task

| I need to... | Go to |
|---|---|
| Configure MyChart access rules | [[Epic-MyChart-Rules]] |
| Set session timeout | [[Epic-Session-Definitions-Limits]] |
| Build a Reporting Workbench report | [[Epic-Reporting-Workbench-Slicer-Dicer]] |
| Add a provider to the masterfile | [[Epic-Provider-Masterfile]] |
| Build a questionnaire | [[Epic-Questionnaire-Build]] |
| Understand pool/block scheduling | [[Epic-Pools-Blocks-Modifiers]] |
| Troubleshoot patient self-service | [[Epic-Patient-Self-Service]] |
| Add/edit SmartPhrases | [[Epic-SmartText-SmartPhrases]] |
| Set up a new department | [[Epic-Department-Build-Facility-Structure]] |
| Understand clinic workflow | [[Epic-Clinic-Workflow]] |

---

## References
- Epic UserWeb: https://userweb.epic.com (requires Epic organization credentials)
- Epic on FHIR / Open.epic.com: https://open.epic.com
- Epic Systems: https://www.epic.com

---
<- [[Epic-MOC]]