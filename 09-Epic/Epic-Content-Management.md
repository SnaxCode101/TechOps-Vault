---
title: Epic Content Management
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - content-management
  - order-sets
  - preference-lists
  - navigator
  - build
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic build guides, Epic content
  management documentation
difficulty: intermediate
---

# 📋 Epic Content Management

## Overview

Content Management in Epic refers to the administration of **clinical content** — the building blocks that appear in a clinician's workflow inside Hyperspace. This includes order sets, preference lists, navigator sections, SmartTools, clinical decision support rules, and more. As an analyst, you build, modify, and govern clinical content in collaboration with clinical stakeholders.

> ⚠️ **Flag:** Epic content governance varies significantly between organizations. Many orgs have a formal Clinical Informatics / Content Committee that approves content changes. Always follow your organization's content governance workflow before pushing content to production. Epic UserWeb references should be verified per your installed version.

---

## Core Content Types

### Content Hierarchy Overview
```
Clinical Content in Epic
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Order Sets              — grouped orders for a clinical scenario
Preference Lists        — provider's customized order shortcuts
Navigator Sections      — encounter documentation panels
SmartTools             — SmartTexts, SmartPhrases, SmartLinks, SmartLists
Clinical Decision Rules — Best Practice Advisories (BPAs), alerts
Note Templates          — structured documentation templates
Care Plans              — patient plan of care content
Patient Education       — after-visit education handouts
Questionnaires          — patient-facing data collection forms
```

---

## Order Sets

### What Are Order Sets?
Order Sets are **pre-built groups of orders** designed for a specific clinical scenario (e.g., "Sepsis Bundle", "Chest Pain Admission Orders", "Pre-op Knee Replacement"). They allow providers to place multiple orders with one action, promoting standardization.

### Order Set Record Type
- Master file: **Order Set (ORS)** records in Chronicles
- Build interface: **Epic > Order Set Editor** (or via applicable build environment)

### Order Set Anatomy
| Component | Description |
|---|---|
| **Header** | Name, type, associated department/specialty |
| **Sections** | Logical groupings within the set (e.g., "Medications", "Labs", "Nursing") |
| **Order lines** | Individual orders within each section |
| **Default selections** | Pre-checked vs. opt-in orders |
| **Required fields** | Fields provider must fill in before signing |
| **Conditional display** | Orders shown/hidden based on patient data |

### Order Set Configuration Steps
1. Navigate to **Order Set Editor** in build environment (Test first)
2. Create new ORS record or clone existing
3. Add sections and order lines (medications, labs, imaging, nursing, referrals)
4. Set default check status for each order
5. Add any required fields (e.g., dose, frequency, indication)
6. Associate with relevant departments and specialties
7. Test in Test/Train environment with clinical stakeholder
8. Follow governance process → promote to Production via Update Set

### Order Set Best Practices
- Keep sets focused on a single clinical use case
- Minimize "always checked" orders — require intentional selection
- Review and retire outdated sets regularly (joint committee recommendation)
- Document author, last review date, and clinical owner in the record
- Version control: note change history in record or change management system

---

## Preference Lists

### What Are Preference Lists?
Preference Lists are **provider-specific (or department-specific) shortcut lists** of their most commonly used orders, diagnoses, medications, and SmartPhrases. They appear in the ordering workflow, speeding up documentation.

### Types of Preference Lists
| Type | Scope | Build By |
|---|---|---|
| **Personal preference lists** | Individual provider | Provider self-service or analyst on behalf |
| **Department preference lists** | Shared across a department | Analyst / department champion |
| **Specialty preference lists** | Shared for a clinical specialty | Analyst / clinical informatics |

### Preference List Record
- Contains ordered items: diagnoses, medications, orders, SmartPhrases
- Can be nested (groupers within a list)
- Linked to provider's SER record or department DEP record

### Building a Department Preference List
1. Navigate to **Epic > Preference List Editor** (or Chronicles > Preference List record)
2. Create new PLI record
3. Add items: diagnoses (ICD-10), medications, order names
4. Set display order
5. Associate with DEP record or SER records
6. Test with clinical staff
7. Promote to production per governance

### Provider Self-Service
Providers can maintain their own preference lists directly in Hyperspace:
- While placing an order: right-click > Add to Preference List
- Via **Personalize** menu in Hyperspace
- Analyst may assist if provider cannot manage independently

---

## Navigator Sections

### What Are Navigator Sections?
The **Navigator** in Epic's Hyperspace is the left-side panel during an encounter. It contains sections that guide the clinical workflow (e.g., "Chief Complaint", "Vitals", "HPI", "Assessment & Plan"). Each section is a configurable content component.

### Navigator Anatomy
```
Encounter Navigator (Left Panel)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
┌─ Chief Complaint
├─ Vitals
├─ Allergies / Medications Review
├─ HPI (History of Present Illness)
├─ ROS (Review of Systems)
├─ Exam
├─ Diagnosis
├─ Assessment & Plan
│    ├─ Problem List
│    └─ Orders (linked to order sets)
├─ Patient Education
└─ After Visit Summary
```

### Navigator Configuration
- Navigator sections are configured per **encounter type** and **department specialty**
- Build path: **Epic > Navigator Configuration** (varies by version)
- Sections can be required, optional, or hidden based on encounter type
- Conditional sections: appear based on patient data (e.g., show pregnancy section if female of childbearing age)

### Section Types
| Section Type | Description |
|---|---|
| **Data entry** | Provider fills in structured data (vitals, HPI, ROS) |
| **SmartForm** | Structured documentation form embedded in navigator |
| **Order sections** | Links to order sets or preference lists |
| **Decision support** | BPA alerts, care gap reminders |
| **Read-only display** | Shows existing data (med list, problem list) |

---

## Best Practice Advisories (BPAs)

### What Are BPAs?
Best Practice Advisories are **clinical decision support alerts** that fire during the encounter workflow based on patient data and clinician actions. Examples: "Patient due for flu vaccine", "Opioid prescription — PDMP check required", "Sepsis criteria met — consider order set".

### BPA Components
| Component | Description |
|---|---|
| **Trigger** | When the BPA fires (order entry, chart open, sign encounter) |
| **Criteria** | Patient/encounter conditions that must be met |
| **Alert message** | Text shown to provider |
| **Actions** | One-click options (Accept, Defer with reason, Acknowledge) |
| **Reason codes** | Required if overriding the advisory |
| **Reporting** | Override rate tracked for quality reporting |

### BPA Governance Considerations
- High-frequency BPAs that are frequently overridden = **alert fatigue**
- Regular review of override rates is a clinical informatics best practice
- CMS/TJC quality measures may require specific BPAs to remain active
- [VERIFY: Epic UserWeb — BPA Build Guide]

---

## Patient Education Content

### Overview
Epic integrates with patient education content vendors (historically **Krames On-FHIR**, **Healthwise**, **Micromedex**) to deliver after-visit education.

### Management Tasks
- Update educational topic associations per diagnosis/procedure
- Review education assigned to encounter/discharge
- Ensure AVS (After Visit Summary) includes relevant patient education
- Configure patient education handouts for MyChart messaging

---

## Common Scenarios & Job-Aid

### Scenario: Provider Requests New Order Set
1. Receive request via ServiceNow ticket or clinical informatics intake
2. Gather requirements: clinical use case, required orders, optional orders, default selections, target specialty/department
3. Build in Test environment
4. Clinical review cycle with requesting provider and relevant stakeholders
5. Obtain governance approval (Clinical Content Committee or equivalent)
6. Test in Train environment with end users
7. Promote to production
8. Communicate go-live to affected staff
9. Document in change management system

### Scenario: Order Set Is Out of Date — Clinical Stakeholder Requests Update
1. Identify current version in production
2. Clone/copy to Test environment for editing
3. Apply requested changes
4. Review with clinical owner
5. Governance approval for changes
6. Deploy to production; retire or archive old version

### Scenario: Navigator Section Not Appearing for Provider
1. Confirm provider's department and encounter type match the navigator configuration
2. Check if section is conditionally hidden (based on patient data)
3. Verify provider's security class allows access to that section
4. Check if section was accidentally deactivated in build

---

## Related Notes
- Epic-SmartText-SmartPhrases
- Epic-Department-Build-Facility-Structure
- Epic-Provider-Masterfile
- Epic-Questionnaire-Build

## References
- Epic UserWeb — Order Set Build and Configuration
- Epic UserWeb — Best Practice Advisory Build
- Epic UserWeb — Navigator Configuration
- Epic UserWeb — Clinical Content Governance Guide

---
<- [[Epic-MOC]]