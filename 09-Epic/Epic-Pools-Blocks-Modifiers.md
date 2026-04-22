---
title: Epic Pools, Blocks & Modifiers
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - scheduling
  - pools
  - blocks
  - modifiers
  - templates
  - visit-types
  - EAP
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic Scheduling configuration guides
difficulty: intermediate
---

# 📅 Epic Pools, Blocks & Modifiers

## Overview

Epic's scheduling system uses **templates, pools, blocks, and modifiers** to define when providers and departments are available, what types of appointments can be booked, and who can book them. Mastering these concepts is essential for scheduling build, troubleshooting appointment access, and supporting complex scheduling scenarios like multi-specialty clinics, group visits, and urgent care.

> ⚠️ **Flag:** Epic scheduling terminology and build paths vary by Epic version and organization. Some orgs use advanced scheduling features (epic scheduling optimization, Smart Scheduling) while others use basic templates. Verify build steps in your Test environment. Consult Epic UserWeb for version-specific guidance.

---

## Core Concepts

### Scheduling Hierarchy
```
Provider or Department Schedule
  └── Schedule Template (SCH record)
        └── Session (block of time on a day)
              └── Slots (individual appointment openings)
                    └── Visit Types (EAP — what can be booked in each slot)
```

### Key Record Types in Scheduling
| Record | Abbreviation | Purpose |
|---|---|---|
| Visit Type | EAP | Defines the type of appointment (duration, who can book, rules) |
| Schedule Template | SCH | Defines the recurring schedule pattern for a provider/department |
| Scheduling Pool | Pool | Group of providers/resources available for next-available scheduling |
| Block | (within SCH) | Reserved time on a template for specific purposes |
| Modifier | (EAP-level) | Adjusts how a visit type behaves in specific contexts |

---

## Visit Types (EAP Records)

### What Is an EAP Record?
An **EAP** (Epic Appointment — historically named from the table prefix) record defines a type of appointment. It is the foundational scheduling record that everything else references.

### Key EAP Fields
| Field | Description |
|---|---|
| Name | Visit type display name (e.g., "New Patient – 60 min", "Follow Up – 20 min") |
| Duration | Length of the appointment slot in minutes |
| Scheduling type | Open / Pool / Direct provider scheduling |
| Patient-facing name | Name shown to patient in MyChart (may differ from internal name) |
| Online scheduling | Enabled/disabled for MyChart patient booking |
| Eligible departments | Which departments this visit type can be used in |
| New/established patient | Restrict to new patients, established, or both |
| Age range restriction | Pediatric-only, adult-only, etc. |
| Referral required | Flag for insurance/referral authorization requirement |
| Instructions | Pre-appointment instructions shown to patient |
| Associated questionnaire | Trigger questionnaire at booking or eCheck-In |

### Visit Type Naming Conventions
Organizations typically use structured naming:
`[Type] - [Duration] - [Specialty]`
Example: `NEW PT - 60 - CARDIOLOGY`, `FOLLOW UP - 20 - PRIMARY CARE`

### EAP Modifiers
Modifiers adjust how a visit type behaves in a specific scheduling context. Common modifier use cases:
- **Telehealth modifier** — marks a visit type as a video visit, enabling telehealth workflow
- **Department override** — same visit type but different duration at one specific department
- **Provider override** — specific provider gets a different duration for a common visit type
- **Insurance modifier** — rules for specific payer types
- [VERIFY: Epic UserWeb — EAP Modifier Configuration Guide for your version]

---

## Schedule Templates (SCH Records)

### What Is a Schedule Template?
A **schedule template** defines the recurring schedule pattern for a provider, department, or resource. It lays out what days/times are available and which visit types can be booked in each slot.

### Template Anatomy
```
Template: Dr. Smith — Primary Care (Mon–Fri)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Monday:
  08:00 – 09:00  │  3 × 20-min Follow Up slots
  09:00 – 10:00  │  1 × 60-min New Patient slot
  10:00 – 11:30  │  BLOCK: Procedure time (no booking)
  11:30 – 12:00  │  2 × 20-min Follow Up slots
  12:00 – 13:00  │  BREAK (no booking)
  13:00 – 17:00  │  8 × 30-min Mixed slots
```

### Template Configuration Steps
1. Navigate to **Epic > Scheduling > Template Editor** (or applicable build tool)
2. Create new SCH record or clone existing
3. Assign to provider (SER record) or department (DEP record) or resource
4. Define days/hours of availability
5. Add sessions (time blocks within each day)
6. Configure each session: visit types allowed, number of slots, duration
7. Add blocks for protected time (meetings, admin time, procedures)
8. Set template effective dates (start date, end date or open-ended)
9. Associate with department

### Template Types
| Type | Usage |
|---|---|
| **Provider template** | Schedule for a specific provider (SER) |
| **Department template** | Block scheduling for a whole department |
| **Resource template** | Equipment or room scheduling (e.g., MRI machine, procedure room) |
| **Group visit template** | Multiple patients in one appointment slot |

---

## Pools

### What Is a Scheduling Pool?
A **scheduling pool** is a group of providers, departments, or resources that share appointment capacity. When a patient books via a pool, they get the **next available** appointment with any pool member, rather than a specific provider.

### Pool Use Cases
| Use Case | Pool Type |
|---|---|
| Next Available Primary Care | All PCP providers in a clinic share a pool |
| Nurse advice line | A group of nurses available for phone appointments |
| Urgent Care / Walk-in | Department pool for same-day availability |
| Message pools | Providers who receive MyChart messages from patients (messaging pool) |
| Referral pools | Specialty departments accepting referral scheduling |

### Scheduling Pool vs. Messaging Pool
| Type | Purpose |
|---|---|
| **Scheduling Pool** | Groups providers/resources for appointment availability |
| **Messaging Pool (In Basket Pool)** | Groups staff for routing and responding to MyChart messages |

> Both types of pools exist in Epic. Scheduling pools drive appointment booking; messaging pools drive communication routing. They are distinct but related concepts.

### Building a Scheduling Pool
1. Navigate to **Pool Editor** in Epic build environment
2. Create or edit pool record
3. Add pool members (SER records for providers, DEP records for departments, or resource records)
4. Define pool visibility rules (which departments/visit types use this pool)
5. Set capacity rules (slots per provider, pool-level maximums)
6. Test: verify next-available booking presents pool members correctly

### Messaging Pool Configuration
1. Navigate to **In Basket Pool configuration** (via Epic > InBasket > Pool Management or DEP record)
2. Create or edit messaging pool
3. Add pool members (EMP/staff records — not just providers)
4. Assign pool to department(s)
5. Configure routing rules (which message types route to which pool)
6. Test: send a test MyChart message; verify it routes to correct pool

---

## Blocks

### What Are Schedule Blocks?
A **block** on a schedule template is a **reserved time period** that prevents regular appointment booking in that time. Blocks are used for:
- Provider meetings / administrative time
- Procedure time (longer cases that need a protected slot)
- Hold slots (reserved for specific patient types or urgent add-ons)
- Lunch/breaks
- Call/on-call periods

### Block Types (Common)
| Block Type | Description |
|---|---|
| **Hard block** | No appointments can be booked; completely closed |
| **Soft block** | Default closed, but overridable by scheduler with appropriate security |
| **Hold block** | Slot held for a specific visit type (e.g., "urgent same-day only") |
| **Wave block** | Multiple patients booked in the same time window (wave scheduling) |
| **Resource block** | Reserves a room or piece of equipment alongside provider time |

### Configuring Blocks on a Template
1. Open SCH template record in Template Editor
2. Select the day/time range to block
3. Choose block type and reason/label
4. Set override permissions (who can schedule into blocked time)
5. Apply to future dates or retroactively as needed
6. Test to confirm scheduling behavior

### Adding a One-Time Block (Provider Out of Office)
For ad hoc closures (sick day, conference, vacation):
1. Navigate to **Scheduling > Hold/Release Schedule** or use **Schedule Block** feature in Hyperspace
2. Select provider, date range
3. Block all time or specific sessions
4. Handle/reschedule any pre-existing appointments in the blocked time
5. Communicate change to scheduling staff

---

## Common Scenarios & Job-Aid

### Scenario: Patient Calls — Can't Book Specific Visit Type Online
1. Check EAP record — is online scheduling enabled?
2. Check patient eligibility rules (new vs. established, age range)
3. Check template — are there open slots for that visit type in the relevant date range?
4. Check if there's an active block on the schedule
5. Verify provider is in an active pool if using pool scheduling
6. Test as a test patient in Train environment

### Scenario: Provider's Schedule Shows as Full But Clinic Manager Says There Should Be Openings
1. Check template effective dates — is the correct template active?
2. Check for blocks that may be consuming availability
3. Check overbooking limits — is the system preventing additional booking beyond template capacity?
4. Check if hold slots are consuming capacity that's visible to staff but not patient-facing
5. Run a scheduling utilization report for the provider/date range

### Scenario: New Provider Joining — Need to Build Their Schedule
1. Ensure SER (Provider Masterfile) record is built first — see [[Epic-Provider-Masterfile]]
2. Identify visit types they will see (EAP records) — build or reuse existing
3. Build schedule template (SCH) with their availability
4. Associate template with their SER record and department
5. If shared pool: add to appropriate scheduling pool
6. If online scheduling: enable on EAP and SER records
7. Test in Train: book test appointments, verify MyChart visibility
8. Go live with provider start date

### Scenario: Adding an Urgent Same-Day Pool
1. Create or identify visit type (EAP) for urgent/same-day visits
2. Identify provider(s) or department participating in urgent pool
3. Create scheduling pool with those providers
4. Configure templates with hold slots for urgent visit type
5. Set appropriate online scheduling rules (staff-only or patient-facing)
6. Train scheduling staff on pool usage

---

## Related Notes
- Epic-Department-Build-Facility-Structure
- Epic-Provider-Masterfile
- Epic-Patient-Self-Service
- Epic-MyChart-Rules

## References
- Epic UserWeb — Scheduling Template Build Guide
- Epic UserWeb — Pool Configuration Guide
- Epic UserWeb — EAP (Visit Type) Build and Configuration
- Epic UserWeb — Schedule Blocks and Hold Configuration

---
<- [[Epic-MOC]]