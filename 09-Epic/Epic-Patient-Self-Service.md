---
title: Epic Patient Self-Service
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - patient-self-service
  - echeck-in
  - online-scheduling
  - mychart
  - questionnaire
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic MyChart and self-service build guides
difficulty: intermediate
---

# 📱 Epic Patient Self-Service

## Overview

Patient self-service encompasses all the tools that allow patients to interact with their healthcare digitally, without requiring staff assistance. In Epic, this primarily includes **MyChart online scheduling**, **eCheck-In**, **questionnaires**, **MyChart messaging**, **bill pay**, and **patient-facing results/records**. Building and supporting these features reduces staff burden, improves patient satisfaction, and supports operational efficiency.

> ⚠️ **Flag:** Epic self-service feature availability depends on your organization's purchased modules and configured integrations. Not all organizations have all features. Verify feature availability in your Epic Test environment or with your Epic account team. Feature names and navigation may differ by Epic version.

---

## Core Concepts

### Self-Service Feature Map
| Feature | Patient Action | Epic Component |
|---|---|---|
| Online Scheduling | Book/cancel/reschedule appointments | MyChart + Scheduling Configuration |
| eCheck-In | Complete pre-visit tasks (demographics, questionnaires, copay) | MyChart eCheck-In module |
| Questionnaires | Complete health history forms, pre-visit intake | QNS (Questionnaire) records |
| Secure Messaging | Message care team | MyChart Messaging / In Basket |
| Bill Pay | Pay balances online | MyChart + billing integration |
| Results Review | View labs, imaging results | MyChart Results |
| Appointment Reminders | Receive automated reminders | MyChart + reminder configuration |
| Video Visits | Launch telehealth from MyChart | MyChart + telehealth integration |
| Proxy Access | Parent/caregiver managing patient's health | MyChart Proxy |

---

## Online Scheduling

### Overview
Patients can schedule, cancel, or reschedule appointments directly in MyChart without calling the clinic.

### Requirements to Enable Online Scheduling
For a visit type to be available for online scheduling:
1. **EAP (Visit Type) record** must have online scheduling enabled
2. **Department (DEP) record** must have MyChart online scheduling enabled
3. **Schedule template** must have open slots for that visit type
4. **Provider SER record** must allow online scheduling (for direct provider scheduling)
5. Patient must have an active MyChart account (or guest scheduling if enabled)

### Online Scheduling Configuration — EAP Record
Key fields in the EAP (Visit Type / EAP) record:
| Field | Description |
|---|---|
| Allow online scheduling | Enable for patient self-service booking |
| Scheduling type | Direct (specific provider) or pool (next available) |
| Patient instructions | Text shown to patient during booking (preparation, what to bring) |
| Duration | Visit length (determines slots used on template) |
| Eligibility rules | Restrict to: established patients only, new patients only, specific age range |
| Require questionnaire | Trigger questionnaire as part of booking |
| Referral required | Flag if referral authorization is needed before booking |

### Guest Scheduling (No MyChart Account Required)
- Some organizations enable scheduling without requiring MyChart login
- Patient provides name/DOB/contact info; system matches or creates patient record
- Useful for new patients who haven't yet activated MyChart

### Open Access / Direct Access Scheduling
- **Open access** — many slots available on the same day or next day; patient books when they need
- **Direct scheduling** — patient books directly with a specific provider vs. going through a referral
- Configuration: template design + EAP rules; see [[Epic-Pools-Blocks-Modifiers]]

---

## eCheck-In

### What Is eCheck-In?
eCheck-In is the digital pre-visit process where patients complete administrative and clinical tasks before arriving for their appointment. It replaces clipboard intake forms at the front desk.

### eCheck-In Workflow
```
Patient receives appointment reminder
  └── Link to eCheck-In (MyChart notification, email, or SMS)
        └── Patient opens eCheck-In
              ├── Confirm/update demographics
              ├── Confirm/update insurance information
              ├── Confirm/update pharmacy preference
              ├── Review/sign consent forms
              ├── Complete health questionnaire
              ├── Pay copay / outstanding balance (if billing enabled)
              └── Confirm arrival → staff notified
```

### eCheck-In Configuration
- **eCheck-In window** — configurable number of days before appointment when eCheck-In becomes available (typically 3–7 days prior)
- **Tasks shown** — configurable by department/visit type; not all tasks need to appear for all visit types
- **Required vs. optional tasks** — demographics update may be required; copay collection may be optional
- **Questionnaires** — linked to eCheck-In by visit type (see [[Epic-Questionnaire-Build]])

### eCheck-In Status Tracking
Staff can see which patients have completed eCheck-In via:
- **Appointment Desk** (scheduling view) — eCheck-In status column
- **Dashboard tiles** — configured for department/unit
- **Room and Arrival** workflow — rooming staff can see completed vs. pending eCheck-In items

### eCheck-In Troubleshooting
| Issue | Check |
|---|---|
| Patient doesn't see eCheck-In link | Is appointment type configured for eCheck-In? Is it within the eCheck-In window? |
| Patient can't update demographics | Is demographics update enabled for that department? |
| Questionnaire not appearing | Is questionnaire linked to visit type or department? See [[Epic-Questionnaire-Build]] |
| Copay not appearing | Is billing integration active? Is patient's balance above threshold? |

---

## Patient Appointment Reminders

### Reminder Types
| Type | Method | Configuration |
|---|---|---|
| **MyChart notification** | In-app message | Automated via MyChart rules |
| **Email reminder** | Email to address on file | Automated; timing configurable |
| **SMS/text reminder** | Text to mobile number | Requires opt-in + messaging vendor integration |
| **Automated voice call** | Phone call | Requires telephony integration |

### Reminder Configuration
- Reminder timing: typically 3 days and 1 day before appointment (configurable)
- Reminder content: includes provider name, date/time, location, eCheck-In link, visit preparation instructions
- Opt-out: patients can opt out of reminders in MyChart preferences
- Staff can manually send appointment reminders from Hyperspace

### Two-Way Texting / Appointment Confirmation
Some Epic configurations support **two-way texting** where patients can reply YES/NO to confirm/cancel appointments via SMS. This requires third-party integration (e.g., Relatient, Nuance, Klara) or Epic's native solution depending on version. [VERIFY: Epic UserWeb — Appointment Reminder and Two-Way Messaging]

---

## MyChart Bill Pay

### Patient-Facing Billing Features
| Feature | Description |
|---|---|
| View statements | Patient sees itemized account statements |
| Pay balance | Credit card, debit, HSA/FSA payment |
| Payment plans | Set up recurring payment plan |
| Financial assistance | Apply for charity care / financial assistance through MyChart |
| Cost Estimates | Pre-service cost estimation (Epic Cost Estimator) |

### Configuration Requirements
- Billing integration must be active between Epic's billing modules (HB and PB) and MyChart
- Payment processor integration required (e.g., Sphere, InstaMed, or organization's merchant)
- Cost estimator requires charge master and payer contract data [VERIFY: Epic UserWeb]

---

## Common Scenarios & Job-Aid

### Scenario: Patients Complain They Can't Book Online for a Specific Provider
1. Check EAP (visit type) — is online scheduling enabled?
2. Check DEP (department) — is MyChart online scheduling enabled?
3. Check the provider's SER record — is online scheduling enabled?
4. Check schedule template — are there open slots of the correct visit type?
5. Check patient eligibility rules — is the patient new or established? Does rule match?
6. Check if the visit type requires a referral that hasn't been entered
7. Test in Train environment as a test patient

### Scenario: eCheck-In Tasks Not Appearing for Patients
1. Is appointment within the eCheck-In window (e.g., only within 7 days of appointment)?
2. Is the department configured to show eCheck-In?
3. Is the visit type configured to trigger eCheck-In tasks?
4. Are questionnaires properly linked to the visit type?
5. Has patient already completed eCheck-In (tasks may show as complete, not pending)?

### Scenario: Patient Says They're Getting Too Many Reminders
1. Check reminder rules — are multiple systems sending reminders (MyChart + external vendor)?
2. Verify patient has not been enrolled in a third-party reminder system separately
3. Advise patient on how to manage communication preferences in MyChart
4. Review with operational team whether reminder cadence is appropriate

---

## Related Notes
- Epic-MyChart-Rules
- Epic-Questionnaire-Build
- Epic-Pools-Blocks-Modifiers
- Epic-Department-Build-Facility-Structure

## References
- Epic UserWeb — MyChart eCheck-In Configuration Guide
- Epic UserWeb — Online Scheduling Build Guide
- Epic UserWeb — Appointment Reminder Configuration
- Epic UserWeb — Bill Pay and Financial Experience Configuration

---
<- [[Epic-MOC]]