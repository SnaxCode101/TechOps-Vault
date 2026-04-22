---
title: Epic Questionnaire Build & Support
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - questionnaire
  - QNS
  - patient-intake
  - forms
  - smartform
  - echeck-in
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic Questionnaire and SmartForm build guides
difficulty: intermediate
---

# 📝 Epic Questionnaire Build & Support

## Overview

Epic Questionnaires are **patient-facing digital forms** used to collect health information before or during a visit. They replace paper intake forms, pre-visit questionnaires, and health history forms. Questionnaires can be completed via **MyChart** (before arriving), during **eCheck-In**, at a **kiosk**, or on a **tablet in the waiting room**. Build and configuration of questionnaires is a common analyst task tied to both patient experience and clinical data quality.

> ⚠️ **Flag:** Epic's questionnaire framework has evolved significantly across versions (SmartForms vs. QNS-based questionnaires vs. FHIR Questionnaires). Your organization may use one or multiple approaches. Verify which system your org uses and reference the appropriate Epic UserWeb guide for your version.

---

## Core Concepts

### Questionnaire Types in Epic
| Type | Description | When Used |
|---|---|---|
| **QNS-based Questionnaire** | Traditional Epic questionnaire record | MyChart self-service, kiosk, eCheck-In |
| **SmartForm** | Encounter-embedded structured data entry form | Used by clinicians during encounter; can also be patient-facing |
| **FHIR Questionnaire** | Modern standards-based form (JSON) | MyChart in newer Epic versions; part of open standards push |
| **Health Questionnaire (HQ)** | Specific type for health history / social history | Pre-visit intake for health history sections |

> For the scope of an analyst supporting MyChart/self-service, **QNS questionnaires** and the **Health Questionnaire** type are most commonly the build targets.

---

## QNS Record Structure

### Questionnaire (QNS) Anatomy
A QNS record defines:
| Component | Description |
|---|---|
| **Title** | Name shown to patient and staff |
| **Instructions** | Introductory text shown at top of questionnaire |
| **Questions** | Individual items in the form |
| **Question type** | Free text, yes/no, multiple choice, date, numeric, Likert scale |
| **Branching logic** | Show/hide follow-up questions based on prior answers |
| **Required questions** | Questions patient must answer before submitting |
| **Response storage** | Where answers map in the patient's chart |
| **Scoring** | For validated instruments (PHQ-9, GAD-7, etc.) — automatic score calculation |
| **Associated visit types** | Which EAP visit types trigger this questionnaire |
| **Trigger rules** | When to send (at booking, X days before, eCheck-In, etc.) |

### Question Types
| Type | Description | Example |
|---|---|---|
| Free text | Open-ended text field | "Describe your chief complaint" |
| Yes/No | Binary choice | "Do you smoke?" |
| Single select | Radio button choice from list | "Rate your pain 1–10" |
| Multi-select | Checkbox list | "Which symptoms are you experiencing?" |
| Date | Date picker | "When did your symptoms start?" |
| Numeric | Number entry | "How many drinks per week?" |
| Likert scale | Scale rating | "How would you rate your quality of life?" |
| Instruction | Non-question text block | Section headers, instructions |

---

## Validated Instruments in Epic

Epic has built-in support for standardized, validated screening tools. These map directly to structured chart data. Common examples:
| Instrument | Full Name | Use Case |
|---|---|---|
| **PHQ-9** | Patient Health Questionnaire-9 | Depression screening |
| **PHQ-2** | PHQ-2 (shortened) | Initial depression screen |
| **GAD-7** | Generalized Anxiety Disorder-7 | Anxiety screening |
| **AUDIT-C** | Alcohol Use Disorders Identification Test | Alcohol use screening |
| **DAST-10** | Drug Abuse Screening Test | Drug use screening |
| **CAGE-AID** | CAGE with adaptation for drugs | Substance use |
| **EPDS** | Edinburgh Postnatal Depression Scale | Post-partum depression |
| **PSQ-3** | Patient Stress Questionnaire | Stress screening |
| **CSSRS** | Columbia Suicide Severity Rating Scale | Suicide risk |

> For validated instruments: do NOT modify the question text, order, or scoring logic. Altering validated tools invalidates their clinical validity. Build these using Epic's supplied templates and verify against your clinical informatics team.

---

## Building a Questionnaire — Step-by-Step

### Step 1: Requirements Gathering
Before building, document:
- What information will be collected?
- Is this a validated instrument (use Epic's supplied record) or custom form?
- When is it triggered? (at booking, eCheck-In, specific visit type)
- What visit types are associated?
- Where do responses map in the chart (structured data fields, social history, HPI)?
- Does it require branching logic?
- Is it for all patients or specific age groups / genders / conditions?

### Step 2: Build in Test Environment
1. Navigate to **Epic > Chronicles > Questionnaire (QNS)** or questionnaire build tool
2. Create new QNS record (or copy existing similar questionnaire)
3. Enter title and patient-facing instructions
4. Add questions in sequence
5. Set question types and response options
6. Configure branching logic (if applicable)
7. Mark required questions
8. Map responses to chart data fields
9. Configure scoring (if validated instrument)
10. Set trigger rules and associated visit types

### Step 3: Associate with Visit Types / eCheck-In
- Link the questionnaire to relevant EAP (visit type) records
- Configure whether it fires at booking, X days before visit, or at eCheck-In
- If multi-questionnaire eCheck-In: set display order

### Step 4: Test
- Log in as test patient in Test/Train environment
- Book appointment with associated visit type
- Complete questionnaire as patient
- Verify in provider Hyperspace that responses appear correctly in chart
- Test branching logic by answering in different ways
- Test with clinical stakeholder

### Step 5: Promote to Production
- Document all changes
- Follow change management governance
- Communicate go-live to clinical and scheduling staff

---

## Questionnaire Triggers & Delivery

### When Can a Questionnaire Fire?
| Trigger Point | Description |
|---|---|
| **At booking** | Immediately when appointment is scheduled (MyChart or staff-booked) |
| **N days before appointment** | Reminder sent with questionnaire link (configurable days) |
| **eCheck-In** | Presented as part of the eCheck-In workflow |
| **Kiosk / Tablet** | Patient completes at check-in on facility device |
| **Order-based** | Triggered by a clinical order (e.g., annual wellness, pre-op) |
| **Interval-based** | Every X months for chronic condition management |

### Delivery Channels
| Channel | Requirement |
|---|---|
| MyChart web | Active MyChart account |
| MyChart mobile app | Active MyChart account + app installed |
| Kiosk | Epic kiosk module configured |
| Tablet (MyChart Bedside) | Epic MyChart Bedside configured |
| Email link | Patient email on file; organization must have this enabled |

---

## Supporting Questionnaires — Common Issues

### Scenario: Patient Not Receiving Questionnaire
1. Confirm appointment visit type is linked to the questionnaire
2. Check trigger timing — is it too early or too late in the trigger window?
3. Verify patient has an active MyChart account
4. Check if questionnaire has already been sent and completed (patient may not recognize it)
5. Check eCheck-In configuration — is the questionnaire enabled for eCheck-In?

### Scenario: Questionnaire Responses Not Appearing in Chart
1. Check QNS record — are response mapping fields correctly configured?
2. Verify questionnaire was fully submitted (not just saved as draft)
3. Check if the provider is looking in the correct chart section (social history, HPI, flowsheet)
4. Review questionnaire build for field mapping errors
5. Escalate to Epic build team if mapping configuration appears correct but data not saving

### Scenario: Provider Requests Changes to an Existing Questionnaire
1. Review the change request with clinical informatics team
2. If it's a validated instrument: consult clinical stakeholder before any modification
3. Build changes in Test
4. Test end-to-end
5. Governance review
6. Promote to production

### Scenario: Need to Retire/Inactivate a Questionnaire
1. Remove questionnaire from associated visit types (so it stops firing)
2. Set questionnaire record to inactive status
3. Verify no pending questionnaires are outstanding for that form
4. Document in change management

---

## Related Notes
- Epic-Patient-Self-Service
- Epic-Content-Management
- Epic-MyChart-Rules
- Epic-Clinic-Workflow

## References
- Epic UserWeb — Questionnaire Build Guide (QNS)
- Epic UserWeb — SmartForm Configuration Guide
- Epic UserWeb — eCheck-In and Questionnaire Integration
- PHQ-9 original source: Kroenke, Spitzer, Williams (2001) — do not modify validated instruments

---
<- [[Epic-MOC]]