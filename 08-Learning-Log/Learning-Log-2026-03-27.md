---
title: Learning Log — 2026-03-27
date: 2026-03-27
tags: |-
  - learning-log
  - session-4
  - epic-ehr
  - healthcare-it
  - vault-build
session: 4
---

# 📓 Learning Log — 2026-03-27 (Session 4)

## Session Summary

**Duration:** Full session  
**Platform:** SecOps-Vault build session (Claude + Obsidian Local REST API via MCP Docker)  
**Goal:** Build Epic EHR knowledge base (09-Epic/), update Dashboard, begin Healthcare-IT section

---

## What Was Built Today

### 09-Epic/ — 12 New Notes (Full Section, New to Vault)

| Note | Topic |
|---|---|
| Epic-Overview | Epic ecosystem, modules, deployment model, UserWeb |
| Epic-MyChart-Rules | Patient portal rules, messaging, proxy access, proxy types |
| Epic-Session-Definitions-Limits | Session types, encounter types, session limits, appointment flow |
| Epic-Reporting-Workbench-Slicer-Dicer | SlicerDicer, Reporting Workbench, report types, filters |
| Epic-Content-Management | SmartForms, preference lists, order sets, groupers |
| Epic-Department-Build-Facility-Structure | Departments, facility hierarchy, ADT structure, bed management |
| Epic-Patient-Self-Service | Appointment self-scheduling, check-in kiosks, eCheck-in, intake forms |
| Epic-Pools-Blocks-Modifiers | Scheduling pools, template blocks, modifiers, slot rules |
| Epic-Questionnaire-Build | Questionnaire types, routing logic, triggers, patient-facing forms |
| Epic-Provider-Masterfile | Provider record, scheduling templates, availability, credentials |
| Epic-SmartText-SmartPhrases | SmartText/SmartPhrase/SmartList build, dot-phrases, macros |
| Epic-Clinic-Workflow | Full clinic day workflow: pre-visit → rooming → visit → close |

### 00-MOC/ — Dashboard.md Updated

- Total note count updated: 77 → 111
- Stats table: new row `09 Epic EHR | 12 | ✅`
- Navigate section: 🏥 Epic EHR (12) section added
- Footer date updated: 2026-03-26 → 2026-03-27
- Overview blurb updated to reflect 111 notes
- Quick-Access table: Epic EHR row added

---

## Vault State After Session 4

| Section | Before | After |
|---|---|---|
| 01-Fundamentals | 23 | 23 |
| 02-Tools | 20 | 20 |
| 03-Labs | 3 | 3 |
| 04-CTF | 2 | 2 |
| 05-Certs | 8 | 8 |
| 06-Reference | 15 | 15 |
| 07-Projects | 2 | 2 |
| 08-Learning-Log | 4 | 5 |
| 09-Epic EHR | 0 | 12 |
| **Total** | **77** | **111** |

---

## Key Concepts Reinforced Today

### Epic EHR — Core Build Concepts
- **Scheduling architecture:** Pools → Blocks → Templates → Modifiers → Slots — the full Epic scheduling stack
- **Session types vs encounter types:** Session = scheduling container; encounter = clinical event; they are linked but distinct
- **SmartText/SmartPhrases:** Dot-phrases (`.name`) auto-expand; SmartLists provide pick-list fields within templates; building vs using them are different skill sets
- **MyChart proxy access:** Types 1–4 define what a proxy can see/do; Proxy Type 1 = full access, Type 4 = billing only
- **Questionnaire routing:** Triggers can be based on encounter reason, age, department, or manual assignment — overlapping triggers require explicit priority rules
- **Provider masterfile vs template:** Provider record (credentials, specialty, NPI) is separate from scheduling template (availability, slots, blocks)
- **Content Management hierarchy:** Order sets → Groupers → Preference lists — preference lists feed order sets, groupers bundle order sets
- **Department vs facility vs location:** Three distinct Epic objects; department is the smallest schedulable unit; all three have separate IDs and build records
- **SlicerDicer:** Self-service analytics tool; uses data models not raw tables; respects security classes; good for operational metrics
- **Clinic workflow cadence:** Pre-visit prep → check-in → rooming → in-room assessment → provider visit → orders/documentation → checkout → post-visit close

### MCP / Vault Infrastructure
- `obsidian_patch_content` heading target fails on emoji-prefixed headings (Unicode encoding issue with MCP API)
- Workaround: delete + `obsidian_append_content` full file rewrite for complex multi-section edits
- Frontmatter `replace` patch works reliably as an alternative for targeted field updates

---

## Next Session Options

| Priority | Task |
|---|---|
| High | Build 10-Healthcare-IT/ section — HL7 v2/FHIR, HIPAA Technical Safeguards |
| High | Add ServiceNow + Epic integration note (ticketing ↔ EHR workflows) |
| Medium | Expand 05-Certs/ — remaining ServiceNow certs (CIS, CAD, etc.) |
| Medium | Add Learning-Log-Index entry for Session 4 |
| Low | Add Lab write-ups: more HTB/THM boxes to 03-Labs/ |
| Low | Build 03-Labs Epic build lab write-up (scheduling template build exercise) |

---
*Log created: 2026-03-27 | Session 4 complete*

---
<- [[Learning-Log-MOC]]