---
title: Epic Primary & Specialty Clinic Workflow
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - clinic-workflow
  - primary-care
  - specialty
  - scheduling
  - rooming
  - documentation
  - AVS
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic ambulatory workflow guides,
  clinical informatics best practices
difficulty: intermediate
---

# 🏥 Epic Primary & Specialty Clinic Workflow

## Overview

Understanding the end-to-end clinic workflow in Epic is essential for an analyst supporting ambulatory environments. Whether you're troubleshooting a scheduling issue, building content, or supporting a go-live, you need to understand how the clinical day flows from pre-visit through documentation, orders, and checkout. Primary care and specialty clinic workflows share the same Hyperspace framework but differ in content, template design, and clinical protocols.

> ⚠️ **Flag:** Workflow steps and Hyperspace activity names can vary between Epic versions and organizational configuration. The workflow below represents typical ambulatory clinic workflow. Verify specific Hyperspace activities, navigator sections, and report names with your Test/Train environment.

---

## Core Concepts: The Ambulatory Visit Lifecycle

```
PRE-VISIT
  ├── Scheduling (staff or patient self-service)
  ├── Appointment reminder sent (email/text/MyChart)
  ├── Pre-visit questionnaire triggered (MyChart)
  └── eCheck-In (patient completes demographics, consent, copay)

DAY OF VISIT — PATIENT ARRIVES
  ├── Check-In (front desk)
  │     ├── Verify demographics and insurance
  │     ├── Collect copay (if not done via eCheck-In)
  │     └── Patient marked "Arrived" in Epic
  │
  ├── Rooming (Medical Assistant / LPN)
  │     ├── Vital signs entered
  │     ├── Chief complaint documented
  │     ├── Medication reconciliation
  │     ├── Allergy review
  │     ├── Questionnaire review / completion
  │     └── Patient marked "Roomed" in Epic
  │
  ├── Provider Visit
  │     ├── HPI / History
  │     ├── Review of Systems (ROS)
  │     ├── Physical Exam
  │     ├── Assessment & Plan
  │     ├── Orders placed (labs, imaging, referrals, medications)
  │     ├── Diagnoses finalized
  │     └── Note signed
  │
  └── Checkout (front desk / MA)
        ├── Follow-up appointment scheduled (if needed)
        ├── Referrals processed
        ├── AVS (After Visit Summary) printed / sent to MyChart
        └── Patient departure

POST-VISIT
  ├── Results reviewed and released (if applicable)
  ├── Patient messaging / follow-up
  ├── Billing / charge capture
  └── Care gap review / chronic disease management
```

---

## Hyperspace Activities — Key Workflow Touchpoints

### Appointment Desk
- **Used by:** Scheduling staff
- **Purpose:** Search for, book, cancel, reschedule appointments
- **Key features:** Slot availability view, provider search, template display, waitlist management
- Analyst support: troubleshoot availability issues, build/modify templates

### Check-In Activity
- **Used by:** Front desk / registration staff
- **Purpose:** Mark patient as arrived, verify demographics, insurance, collect payment
- **Key features:** eCheck-In task review, insurance verification, copay collection
- Analyst support: configure check-in tasks, troubleshoot eCheck-In completion

### Room and Arrival / Rooming Activity
- **Used by:** Medical assistants, LPNs
- **Purpose:** Document rooming tasks — vitals, chief complaint, med rec, allergy review
- **Key features:** Flowsheet entry, medication reconciliation, checklist display
- Analyst support: configure rooming dashboards, flowsheet rows, checklist items

### Navigator (Provider Clinical Activity)
- **Used by:** Physicians, NPs, PAs
- **Purpose:** Complete clinical documentation during the visit
- **Structure:** Left-panel navigator sections guide the workflow
- See [[Epic-Content-Management]] for navigator configuration

### Orders Activity
- **Used by:** Providers (and MA/LPN for protocol/standing orders)
- **Purpose:** Place orders — labs, imaging, medications, referrals, procedures
- **Key features:** Order sets, preference lists, BPA alerts
- See [[Epic-Content-Management]] for order set configuration

### Checkout Activity
- **Used by:** Front desk, MAs
- **Purpose:** Schedule follow-up, process referrals, print/send AVS
- **Key features:** AVS configuration, referral tracking, follow-up scheduling
- Analyst support: configure AVS content, referral process

---

## Differences: Primary Care vs. Specialty Workflows

### Primary Care Clinic
| Characteristic | Description |
|---|---|
| Visit types | Annual wellness, sick visits, chronic disease management, new patient |
| Documentation focus | HPI, ROS, physical exam, preventive care, care gaps |
| Common tools | Care Gap BPAs, health maintenance section, population health tools |
| Scheduling | Often open-access or same-day availability; high volume |
| Panel management | Provider has assigned patient panel; Slicer Dicer used for population health |
| Referral patterns | Refers out to specialty; receives no referrals |
| Common challenges | High volume → SmartPhrase optimization critical; care gap compliance |

### Specialty Clinic
| Characteristic | Description |
|---|---|
| Visit types | New consult, established follow-up, procedure, telehealth, post-op |
| Documentation focus | Specialty-specific assessments, procedure notes, consult letters |
| Common tools | Specialty order sets, procedure documentation, specialty smartphrases |
| Scheduling | Often referral-based; longer appointment times; lower volume |
| Referral patterns | Receives referrals; may refer further to sub-specialties |
| Common challenges | Referral tracking, procedure scheduling, sub-specialty content build |

---

## Key Workflow Concepts for Analysts

### AVS (After Visit Summary)
The **AVS** is the patient's visit summary document — printed or sent to MyChart. It includes:
- Visit date, provider, department
- Chief complaint and diagnoses
- Medication list (active)
- Orders placed (labs, imaging, referrals)
- Patient instructions
- Follow-up appointment
- Patient education materials

**Analyst tasks around AVS:**
- Configure which elements appear on AVS per department/specialty
- Ensure patient education content is current
- Troubleshoot AVS generation failures
- Configure AVS to send to MyChart automatically

### Medication Reconciliation
- Performed during rooming by MA/LPN or during provider visit
- Compares current medication list with what patient reports taking
- Discrepancies must be resolved before orders are placed
- Epic has a structured **Medication Reconciliation activity** for this workflow
- Analyst role: configure med rec activity for departments, troubleshoot discrepancy workflows

### In Basket (Provider Messaging Workflow)
In Basket is Epic's internal messaging and task workflow system for providers and staff. It handles:
- Lab/imaging result notifications
- Patient MyChart messages (routed to messaging pool → provider/staff)
- Prescription refill requests
- Staff messages
- Referral management
- Task assignments

**Analyst tasks around In Basket:**
- Configure message routing pools (who receives which message types)
- Set up auto-routing rules
- Troubleshoot undelivered or misrouted messages
- Configure result release rules (which results go to which pool)
- See also: Messaging Pools in [[Epic-Pools-Blocks-Modifiers]]

### Care Gap / Health Maintenance
- Epic tracks **care gaps** — outstanding preventive care items for a patient (mammogram overdue, vaccination needed, A1c not checked)
- Care gaps are configured by clinical informatics teams and drive BPA alerts and population health outreach
- Analyst role: configure health maintenance records, link to BPAs, set care gap rules for departments

### Referral Workflow
**Sending a referral:**
1. Provider places referral order in the encounter
2. Referral authorization processed (by authorization team or MA)
3. Patient contacts specialist or specialist is contacted via Epic (Care Everywhere or Epic-to-Epic referral)

**Receiving a referral:**
1. Specialist department receives referral in Epic
2. Scheduling contacts patient to book consult appointment
3. Consult visit completed; consult note sent back to referring provider via In Basket

**Analyst role:** configure referral order types, Epic-to-Epic referral routing, referral management dashboards

---

## Telehealth / Video Visit Workflow

### Overview
Many Epic organizations support **video visits** via Epic's integrated telehealth or third-party integrations (Zoom, Microsoft Teams, Doximity).

### Patient-Facing (MyChart) Telehealth Workflow
1. Patient schedules video visit via MyChart (or staff schedules)
2. Patient receives appointment reminder with video visit link
3. Patient joins video visit from MyChart at appointment time
4. Provider joins from Hyperspace video visit activity
5. Clinical documentation proceeds normally within Hyperspace
6. AVS sent to MyChart

### Analyst Telehealth Configuration Tasks
- Configure EAP (visit type) with telehealth/video modifier
- Enable telehealth for department
- Configure video visit platform integration
- Test patient-facing and provider-facing video flows
- Configure appointment reminders with video join instructions

---

## Common Scenarios & Job-Aid

### Scenario: Provider Workflow Is Slow / Too Many Clicks
1. Review navigator configuration — are unnecessary sections present?
2. Evaluate SmartPhrase/SmartText optimization opportunities
3. Review BPA alert frequency — excessive alerts cause alert fatigue → clinical informatics review
4. Evaluate order set use — are order sets available for common visit types?
5. Consider preference list optimization
6. Gather specific pain points from provider (which activities, which steps)

### Scenario: MA Complains Rooming Activity Is Missing Vital Fields
1. Review rooming flowsheet configuration for the department
2. Check if flowsheet rows are present and active for that department
3. Verify MA has the correct security class to document vitals
4. Test in Train environment
5. Escalate to build team if flowsheet row needs to be added

### Scenario: Patient Did Not Receive AVS in MyChart
1. Check if patient has active MyChart account
2. Check encounter type — is AVS configured to auto-send for this visit type?
3. Verify provider signed the note (AVS typically generates after note is signed)
4. Check MyChart messaging preferences (patient may have disabled notifications)
5. Staff can manually send AVS from Hyperspace after the fact

### Scenario: Department Going Live with a New Specialty
1. Requirements gathering: visit types, template design, content (order sets, SmartPhrases, navigator sections), reporting needs
2. Build: DEP record, EAP records, SCH templates, pools, content
3. Provider SER records updated
4. Testing: full workflow test in Train with clinical leads
5. Training: end-user training for scheduling staff, MAs, and providers
6. Go-live support: analyst/analyst lead on-site or available for first 1–2 weeks
7. Post-live review: gather feedback, address issues via tickets

---

## Related Notes
- Epic-Department-Build-Facility-Structure
- Epic-Pools-Blocks-Modifiers
- Epic-Provider-Masterfile
- Epic-Content-Management
- Epic-Patient-Self-Service
- Epic-SmartText-SmartPhrases

## References
- Epic UserWeb — Ambulatory Workflow Guide
- Epic UserWeb — Check-In and Rooming Configuration
- Epic UserWeb — In Basket Configuration Guide
- Epic UserWeb — AVS Configuration Guide
- Epic UserWeb — Telehealth / Video Visit Configuration

---
<- [[Epic-MOC]]