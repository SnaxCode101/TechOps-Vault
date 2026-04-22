---
title: Epic Provider Masterfile — Add/Remove & Settings
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - provider
  - masterfile
  - SER
  - onboarding
  - offboarding
  - scheduling
  - mychart
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic SER build guides, credentialing
  and provider onboarding documentation
difficulty: intermediate
---

# 👤 Epic Provider Masterfile — Add/Remove & Settings

## Overview

The **SER (Provider) Masterfile** is Epic's central record for every clinician, staff member, or entity that participates in clinical workflows, scheduling, prescribing, ordering, or documentation. Building and maintaining SER records is one of the most common and critical analyst tasks. Errors in a provider's SER record affect their ability to see patients, prescribe, order, bill, and communicate via MyChart.

> ⚠️ **Flag:** SER record build involves credentialing, licensing, DEA, NPI, and billing implications. Always coordinate with Credentialing, Medical Staff Office, Billing, and Pharmacy before activating a provider. Many fields have compliance/billing consequences. Verify all field requirements with your organization's Epic build standards and Epic UserWeb for your version.

---

## Core Concepts

### SER Record — What Is It?
A **SER record** (from the historical INI master file prefix `SER`) is Epic's provider/staff record. It stores:
- Identity information (name, credentials, NPI)
- Licensing and DEA number
- Specialty and billing information
- Scheduling settings
- MyChart and messaging settings
- Department/facility associations
- Security and access settings

### SER Record vs. EMP Record
| Record | Purpose |
|---|---|
| **SER** | Clinical provider identity — credentials, NPI, ordering authority, scheduling |
| **EMP** | System user account — login, security class, workstation settings |
A physician typically has **both** a SER record (clinical identity) and an EMP record (system access). Not all SER records need an EMP record (e.g., an external referring provider may have a SER but not log in to your Epic system).

---

## SER Record — Key Fields & Tabs

### General Tab
| Field | Description |
|---|---|
| Name | Provider's full name (legal name for billing) |
| Display name | How name appears in Hyperspace and to patients |
| Provider type | Physician, NP, PA, RN, MA, Resident, etc. |
| Credentials | MD, DO, NP, PA-C, RN, LCSW, etc. |
| Gender | Required for some clinical rules |
| Specialty | Primary clinical specialty (drives content, BPAs) |
| Board certifications | Specialty board certifications |
| Active status | Active / Inactive flag |

### Identifiers Tab
| Field | Description |
|---|---|
| NPI (National Provider Identifier) | 10-digit CMS-issued number — required for billing |
| DEA number | Drug Enforcement Administration number — required for controlled substance prescribing |
| State license numbers | License by state (important for telehealth across state lines) |
| UPIN | Legacy Medicare identifier (mostly obsolete) |
| Organization-internal ID | Your org's internal provider ID |
| Taxonomy code | Provider taxonomy for billing |

> ⚠️ **Flag — Compliance Critical:** NPI and DEA numbers must be entered **exactly** as issued. Errors cause claim denials and pharmacy rejections. Verify directly with the credentialing team before entering. DEA must not be entered until provider has active, unrestricted DEA registration.

### Scheduling Tab
| Field | Description |
|---|---|
| Schedulable | Y/N — can appointments be booked for this provider? |
| Online scheduling enabled | Y/N — allow patient self-scheduling via MyChart |
| Default appointment department | Primary department for scheduling |
| Display on schedule | Y/N |
| Booking rules | Specific rules for this provider's scheduling |

### MyChart / Patient Messaging Tab
| Field | Description |
|---|---|
| MyChart messaging enabled | Y/N — can patients send messages to this provider? |
| Proxy messaging | Can proxy message this provider on patient's behalf? |
| Display in provider directory | Y/N — show provider in patient's MyChart provider list |
| Photo | Provider photo (displayed in MyChart and AVS) |
| Bio | Provider biography displayed to patients in MyChart |
| Patient-facing specialty | Specialty label shown to patients (may differ from clinical specialty) |

### Department Associations
| Setting | Description |
|---|---|
| Primary department | Main clinical department |
| Additional departments | Other departments where provider sees patients |
| Authorizing providers | For mid-level providers — which attendings supervise their orders |

### Billing / Financial Tab
| Field | Description |
|---|---|
| Rendering provider | Is this provider a billable rendering provider? |
| Billing provider group | Which billing group they bill under |
| Fee schedule | Associated fee schedule |
| Provider enrollment | Payer enrollment status (Medicare, Medicaid, commercial) |
| Referring provider | Can they be listed as referring provider on orders? |

---

## Adding a New Provider — Build Checklist

### Pre-Build Requirements (Gather Before Building)
- [ ] Provider's full legal name and credentials
- [ ] Provider type (MD, NP, PA, etc.)
- [ ] NPI number (verify at NPPES: https://npiregistry.cms.hhs.gov)
- [ ] DEA number (if prescribing controlled substances)
- [ ] State medical license number(s)
- [ ] Primary specialty
- [ ] Primary department(s)
- [ ] Start date
- [ ] Whether they need Epic system access (EMP record)
- [ ] Scheduling requirements (schedulable, online scheduling Y/N)
- [ ] MyChart messaging requirements
- [ ] Billing enrollment status
- [ ] Supervising/authorizing providers (for mid-levels)
- [ ] Confirming credentialing approval from Medical Staff Office

### Build Steps in Test/Production
1. **Create SER record** in Chronicles (or applicable build tool)
2. Complete all required fields (name, credentials, NPI, type, specialty)
3. Enter identifiers (NPI, DEA, license)
4. Configure scheduling settings
5. Set department associations
6. Configure MyChart settings (messaging, directory display, photo if available)
7. Configure billing settings
8. If system access needed: create or link EMP record; assign security class
9. Build schedule template (SCH) if provider needs scheduling — see [[Epic-Pools-Blocks-Modifiers]]
10. Add to appropriate scheduling and messaging pools if applicable
11. **Test in Train:** verify scheduling, prescribing (if applicable), MyChart appearance
12. Confirm with Credentialing and Billing that record is correct
13. Activate in Production on or after start date

---

## Modifying a Provider Record

### Common Modifications
| Change | Fields to Update |
|---|---|
| Provider name change (marriage, etc.) | Legal name field + any display name fields |
| New DEA number | DEA number field — verify with pharmacy |
| License renewal/new state | State license fields |
| Adding a new department | Department associations tab |
| Enable/disable online scheduling | Scheduling tab + EAP records |
| Update MyChart bio/photo | MyChart tab |
| Change in supervising provider | Authorizing providers field (mid-levels) |
| Provider promotion (resident → attending) | Provider type, billing settings, ordering authority |

### Modification Process
1. Document the change request (via ServiceNow ticket or internal form)
2. Verify with appropriate authority (credentialing, HR, billing) before modifying compliance fields
3. Make change in Test environment first (for significant changes)
4. Document change
5. Apply to Production
6. Notify affected staff (scheduling, pharmacy, billing) as appropriate

---

## Removing / Inactivating a Provider

### When to Inactivate a SER Record
- Provider leaves the organization
- Provider retires
- Provider's license or DEA is revoked or surrendered
- Provider on extended leave of absence

### Inactivation Checklist
- [ ] Confirm separation/leave date with HR/credentialing
- [ ] Handle all future scheduled appointments (reassign or cancel with patient notification)
- [ ] Remove from all scheduling pools and templates (schedule becomes unavailable)
- [ ] Disable MyChart messaging for provider
- [ ] Remove from MyChart provider directory
- [ ] Disable scheduling (set Schedulable = No)
- [ ] Inactivate EMP record (disable system login if applicable)
- [ ] Notify pharmacy of DEA deactivation if applicable
- [ ] Notify billing of provider billing status change
- [ ] Set SER record inactive status
- [ ] Document in change management
- [ ] Retain record (do NOT delete) — records are needed for audit, billing history, and patient chart reference

> ⚠️ **Critical:** Never delete a SER record. Epic records should be **inactivated**, not deleted. Deletion destroys audit history and can break historical chart references, billing records, and reporting.

---

## Common Scenarios & Job-Aid

### Scenario: New Provider Can't Be Found When Scheduling
1. Check SER record is active and "Schedulable" is set to Yes
2. Check that a schedule template is built and active for that provider
3. Check that the department being scheduled matches the provider's department associations
4. Confirm SER record is linked to correct DEP record
5. Test by searching provider name in scheduling search

### Scenario: Provider's Prescriptions Being Rejected at Pharmacy
1. Verify DEA number is entered correctly in SER record (check against DEA certificate)
2. Verify state license is current and active in record
3. Check if provider has prescribing authority for the drug class
4. Check EPCS (electronic prescribing for controlled substances) enrollment if applicable
5. Escalate to pharmacy and billing teams

### Scenario: Patient Says Their Provider Doesn't Appear in MyChart Provider Directory
1. Check SER record MyChart tab: "Display in provider directory" = Yes?
2. Check "MyChart messaging enabled" = Yes?
3. Verify provider has a linked department that has MyChart enabled
4. Check if patient has ever been seen by this provider (some orgs restrict to established patients)

### Scenario: Provider Is Leaving — Urgent Inactivation Needed
1. Confirm last date of service with HR/credentialing
2. Pull scheduled appointments after termination date
3. Notify scheduling team to reassign/cancel appointments
4. Set termination date in schedule template (no new bookings after date)
5. Process full inactivation checklist on last date of service
6. Follow organizational offboarding SOP

---

## Related Notes
- Epic-Pools-Blocks-Modifiers
- Epic-Department-Build-Facility-Structure
- Epic-MyChart-Rules
- Epic-Clinic-Workflow

## References
- Epic UserWeb — Provider Masterfile (SER) Build Guide
- Epic UserWeb — Provider Onboarding Checklist
- NPPES NPI Registry (public): https://npiregistry.cms.hhs.gov
- DEA Practitioner Registration: https://www.deadiversion.usdoj.gov
- Epic UserWeb — Provider Inactivation Guide

---
<- [[Epic-MOC]]