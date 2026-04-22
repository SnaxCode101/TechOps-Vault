---
title: Epic Department Build & Facility Structure
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - department-build
  - facility
  - location
  - DEP
  - LOC
  - hierarchy
  - scheduling
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic ADT and Scheduling build guides
difficulty: intermediate
---

# 🏗️ Epic Department Build & Facility Structure

## Overview

Epic's facility and department structure underpins **scheduling, ADT (Admission/Discharge/Transfer), clinical documentation, security, reporting**, and **MyChart visibility**. Getting this hierarchy correct is foundational — almost everything in Epic references a department or location. As an analyst, you build and maintain DEP (Department) and LOC (Location) records, understanding how they relate to the physical and organizational structure of your healthcare system.

> ⚠️ **Flag:** Epic's department/location model is complex and highly customizable. Hierarchy relationships, required fields, and naming conventions vary between organizations. Always follow your organization's department naming standards and work with an Epic-credentialed build lead when creating new facilities. Verify build steps against Epic UserWeb for your installed version.

---

## Core Concepts: The Location Hierarchy

### Epic Location Hierarchy (Typical Structure)
```
Facility Group (top-level entity — usually a health system)
  └── Facility (a specific hospital or clinic campus)
        └── Department (a service area, clinic, or unit)
              └── Room (individual exam room, bed, bay)
                    └── Bed (within an inpatient unit)
```

### Record Types
| Record Type | Abbreviation | Description |
|---|---|---|
| **Location** | LOC | Physical location record (building/address level) |
| **Department** | DEP | Service area or clinical unit record |
| **Room** | (within DEP) | Exam rooms, beds configured under department |
| **Facility** | (within LOC/DEP) | Organizational structure grouping |
| **Facility Group** | EAF | Top-level organizational entity |

### Understanding DEP vs. LOC
| Record | Purpose |
|---|---|
| **LOC** | Represents the physical address/building. Used in ADT, for patient location during a visit |
| **DEP** | Represents the administrative/clinical unit. Used in scheduling, documentation, security, reporting |
- One physical building (LOC) can have many departments (DEP)
- A department (DEP) is always associated with a location (LOC)
- Most scheduling and clinical workflows reference the **DEP** record, not LOC directly

---

## Department (DEP) Record — Key Fields

### DEP Record Tabs (Common)
| Tab | Key Fields |
|---|---|
| **General** | Department name, abbreviation, type (clinic, inpatient, ED, etc.) |
| **Location** | Associated LOC record, address |
| **Specialty** | Clinical specialty (drives order sets, templates, preference lists) |
| **Scheduling** | Enable scheduling, default schedule template, online scheduling rules |
| **MyChart** | MyChart enabled Y/N, message routing, online scheduling |
| **ADT** | Enable ADT, unit type, bed configuration |
| **Security** | Department-level security restrictions |
| **Providers** | Associated providers (not always needed — providers associate via SER record) |
| **Contact** | Phone numbers, fax, address for AVS and patient communications |

### Department Types
| Type | Description |
|---|---|
| Outpatient Clinic | Primary and specialty ambulatory visits |
| Inpatient Unit | Hospital floors, ICUs |
| Emergency Department | ED-specific workflows |
| Surgery | OR, procedure rooms |
| Radiology | Imaging departments |
| Lab | Laboratory departments |
| Behavioral Health | Mental health outpatient/inpatient |
| Infusion / Treatment | Oncology, infusion centers |
| Telehealth | Virtual visit departments |

### Department Naming Conventions
Most organizations follow a standard naming convention, e.g.:
`[SITE]-[SPECIALTY]-[TYPE]`
Example: `MH-CARD-CLN` = Main Hospital – Cardiology – Clinic

> Always follow your organization's naming standards. Inconsistent names cause confusion in scheduling templates, reporting, and patient communications.

---

## Creating a New Department — Build Steps

> These steps reflect the general Epic build process. Always build in **Test** first, validate, then promote to **Production**.

### Step 1: Gather Requirements
Before building, document:
- Department name and abbreviation
- Facility/location it belongs to
- Department type (clinic, inpatient, etc.)
- Clinical specialty
- Whether scheduling is needed (online, staff-only, or none)
- Whether MyChart visibility is needed
- Associated providers (SER records)
- Contact info (phone, fax)
- Security requirements

### Step 2: Build the DEP Record in Test
1. Navigate to **Epic > Chronicles > Department (DEP)** or applicable build tool
2. Create new DEP record
3. Fill in required fields: Name, Abbreviation, Type, Specialty, LOC association
4. Configure Scheduling tab: enable scheduling, set template defaults
5. Configure MyChart tab: enable/disable online scheduling and messaging
6. Configure ADT tab if inpatient
7. Add contact information
8. Associate with correct Location (LOC) record

### Step 3: Build Schedule Templates
- Create scheduling templates for the new department (see [[Epic-Pools-Blocks-Modifiers]])
- Associate providers with the department's schedules

### Step 4: Configure Security
- Verify department appears correctly in provider and staff security classes
- Confirm department-level access is granted to appropriate users

### Step 5: Test
- Test scheduling workflow in Test/Train: can staff book appointments?
- Test MyChart: does the department appear for patients where expected?
- Test documentation: do correct navigator sections appear?
- Test reporting: does the department appear in filters?

### Step 6: Promote to Production
- Document all changes
- Follow your organization's change management / update set process
- Go-live communication to affected staff

---

## Location (LOC) Record

### LOC Record Key Fields
| Field | Description |
|---|---|
| Location name | Full name of the physical site |
| Address | Street address (used on AVS, referral letters, patient communications) |
| Phone | Main contact number |
| Fax | Main fax number |
| Type | Hospital, clinic, lab, pharmacy, etc. |
| Facility Group | Which EAF (enterprise entity) this belongs to |
| Time zone | Critical for scheduling — ensures correct appointment times |

### When to Create a New LOC vs. a New DEP
| Situation | Action |
|---|---|
| New physical building / address | Create new LOC |
| New clinical service in existing building | Create new DEP, link to existing LOC |
| Existing service adding a satellite location | Create new DEP, link to new or existing LOC |
| Rebranding a service with no physical change | Update DEP name; LOC may stay same |

---

## Rooms & Beds

### Rooms (Outpatient)
- Exam rooms are configured within a DEP record
- Rooms are used for **resource scheduling** (booking a specific room as a resource alongside provider)
- Room configuration: DEP record > Rooms tab > Add rooms with name, type

### Beds (Inpatient)
- Beds are configured within inpatient DEP records
- Beds are used in **ADT (Admit/Discharge/Transfer)** workflows
- Bed management is critical for capacity management, housekeeping, and patient flow
- ADT integration with bed boards, charge nursing, environmental services

---

## Common Scenarios & Job-Aid

### Scenario: New Satellite Clinic Opening
1. Confirm if new LOC (physical building) record is needed
2. Build new LOC record with address and contact info (if new building)
3. Build new DEP records for each service at the satellite (e.g., primary care, cardiology)
4. Link DEP records to new LOC
5. Build or copy schedule templates
6. Associate providers' SER records with new departments
7. Configure MyChart for new departments
8. Test scheduling, documentation, and MyChart
9. Communicate to staff and go live

### Scenario: Department Name Change (Rebrand)
1. Update DEP record name and abbreviation in Test
2. Verify no hardcoded references to old name in templates, order sets, smart text
3. Update reporting filters/dashboards that reference old department name
4. Update patient-facing materials referencing department name
5. Promote to production
6. Communicate change to staff

### Scenario: Department Needs to be Inactivated (Closing)
1. Remove from all active schedule templates
2. Inactivate scheduling for the DEP (no new appointments)
3. Resolve all future appointments (reschedule or cancel with patient notification)
4. Inactivate DEP record (set inactive flag)
5. Remove MyChart visibility
6. Archive reporting references
7. Document in change management system

---

## Related Notes
- Epic-Pools-Blocks-Modifiers
- Epic-Provider-Masterfile
- Epic-MyChart-Rules
- Epic-Patient-Self-Service

## References
- Epic UserWeb — Department Build Guide
- Epic UserWeb — ADT Department Configuration
- Epic UserWeb — Location Record Build Guide
- Epic UserWeb — MyChart Department Configuration

---
<- [[Epic-MOC]]