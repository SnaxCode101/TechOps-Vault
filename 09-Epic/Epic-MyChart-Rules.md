---
title: Epic MyChart — Rules & Access Configuration
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - mychart
  - access-rules
  - patient-portal
  - security
  - configuration
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic MyChart build guides, Epic Systems
  documentation
difficulty: intermediate
---

# 🌐 Epic MyChart — Rules & Access Configuration

## Overview

MyChart is Epic's patient-facing portal application, available via web browser and mobile app. It allows patients to view health records, message providers, schedule appointments, complete questionnaires, and pay bills. As an analyst, you configure **who can access MyChart**, **what they can see**, and **what actions they can take** through a layered rules system.

> ⚠️ **Flag:** MyChart rule names and specific system property IDs vary between Epic versions (e.g., November 2022, August 2023, etc.). Always verify rule names against your organization's installed version in the Epic UserWeb or directly in your Test/Train environment.

---

## Core Concepts

### MyChart Access Model
MyChart access is controlled by three primary layers:
1. **Patient-level activation** — patient must have an active MyChart account
2. **Department/Facility rules** — which departments expose data/features to MyChart
3. **Feature-level rules** — system properties and security settings controlling individual MyChart features

### MyChart Account Types
| Account Type | Description |
|---|---|
| **Standard Patient Account** | Adult patient managing their own health |
| **Proxy Account** | Adult accessing another patient's record (parent, caregiver, legal guardian) |
| **Adolescent Account** | Teen patient with restricted proxy visibility |
| **Linked Account** | Patient with accounts at multiple Epic organizations (Care Everywhere) |

---

## MyChart Activation Rules

### Activation Methods
Patients can activate MyChart via:
- **Activation Code** — printed on AVS (After Visit Summary) or mailed
- **Instant Activation** — patient activates online with identity verification
- **Staff-Assisted Activation** — front desk activates during check-in
- **Invitation Email** — sent via Epic workflow trigger

### Activation Code Configuration
- Activation codes are generated in **Epic Hyperspace** via the patient's chart or scheduling workflow
- Codes are single-use and time-limited (typically 30–60 days; configurable)
- System property: `MyChart Activation Code Expiration Days` [VERIFY: Epic UserWeb]
- Codes can be printed on: AVS, After Hospital Care Instructions, routing slips

### Identity Verification for Instant Activation
Epic can integrate with identity proofing services (e.g., **Experian**, **LexisNexis**) for online identity verification. Configuration steps:
1. Navigate to **Epic > MyChart > Configuration > Identity Proofing**
2. Enable vendor integration
3. Configure match thresholds (number of questions to pass)
4. Set failure handling (lock after X attempts)

---

## MyChart Access Rules — Key Settings

### Feature-Level Rules (System Properties)
MyChart features are toggled via **Epic System Properties** (INI: `LGL` or `MYC` record types depending on version). Common categories:

#### Messaging Rules
| Setting | Function |
|---|---|
| Enable Patient Messaging | Master on/off for secure messaging |
| Message Routing Rules | Which departments/pools receive messages |
| Auto-response messages | Out-of-office or acknowledgment text |
| Message types allowed | Medical advice, appointment requests, billing questions, etc. |
| Provider opt-out | Allow providers to opt out of direct messaging |

#### Proxy Access Rules
| Setting | Function |
|---|---|
| Proxy relationship types | Configures valid proxy relationship codes (parent, guardian, caregiver) |
| Adult proxy permissions | What a proxy can see/do (full vs. limited) |
| Adolescent breakaway | Age range where teens gain independent access (typically 12–17; state law–dependent) |
| Proxy invitation expiration | Days before proxy invite expires |
| Revoke proxy access | Steps to remove proxy relationship |

> ⚠️ **Flag — Legal Sensitivity:** Adolescent MyChart access rules are **state-law dependent**. Age ranges for adolescent breakaway (where teen can hide records from parent proxy) vary by jurisdiction. Always confirm with your organization's legal/compliance team before configuring. Epic provides a configuration guide tied to state law [VERIFY: Epic UserWeb — Adolescent Confidentiality Configuration Guide].

#### Visit History & Clinical Data Rules
| Setting | Function |
|---|---|
| Notes visibility | Which note types are visible in MyChart (OpenNotes compliance) |
| Lab result release | Immediate vs. delayed release (e.g., 0 days, 3 days, or hold for provider review) |
| Sensitive result hold | Cancer diagnoses, HIV, STI — can be held pending provider call |
| Diagnosis visibility | Which problem list items show in MyChart |
| Medication list | Which medications are visible |
| Immunization visibility | On/off by record type |

> ⚠️ **Flag — 21st Century Cures Act (Info Blocking):** As of April 2021, U.S. providers are generally prohibited from information blocking. Most clinical notes, diagnoses, medications, and labs must be released to patients through MyChart without unnecessary delay. Epic's information blocking compliance features are built into the platform but require organizational policy configuration.

---

## MyChart Security Configuration

### Password & Authentication Rules
| Setting | Typical Default | Notes |
|---|---|---|
| Minimum password length | 8 characters | Configurable |
| Password complexity | Upper + lower + number | Configurable |
| Account lockout threshold | 5 failed attempts | Configurable |
| Account lockout duration | 30 minutes or until admin reset | |
| Two-Factor Authentication (2FA) | Optional/required | SMS or authenticator app |
| Session timeout | 15–30 minutes idle | See [[Epic-Session-Definitions-Limits]] |

### Account Lockout Management
- Locked accounts appear in **Epic > MyChart > Locked Accounts** or via patient chart
- Staff can unlock via: **Epic > Patient > MyChart > Account Management > Unlock Account**
- Admin unlock path: **MyChart Administration > Account Maintenance**
- [VERIFY: Epic UserWeb for exact navigation path per version]

---

## MyChart Department Visibility Rules

### Enabling MyChart for a Department
To make a department's appointments, messages, and results visible in MyChart:
1. Open the **DEP (Department)** record in **Epic > Chronicles > Department Masterfile**
2. Navigate to the **MyChart** tab
3. Set `MyChart Enabled` = Yes
4. Configure which features are department-specific (messaging pools, scheduling, etc.)

### Department-Level Message Routing
- Messages sent from MyChart route to a **message pool** associated with the department
- Configure pool assignments in **DEP record > Messaging > Message Pool**
- Pool members receive and respond to messages; see [[Epic-Pools-Blocks-Modifiers]] for pool configuration

---

## Common Scenarios & Job-Aid

### Scenario: Patient Cannot Log In
1. Confirm MyChart account is active (not locked, not expired)
2. Check for account lockout: **MyChart Administration > Account Lookup**
3. Verify activation code has not expired
4. Check that patient's email matches what's on file
5. If using SSO/federated login, check identity provider status
6. Escalate to Epic team if identity proofing integration is failing

### Scenario: Patient Cannot See Their Lab Results
1. Check lab result release rules for the ordering department
2. Confirm result type is not on a sensitive hold list
3. Verify patient has not opted out of online results
4. Check if result was resulted in a non-MyChart-enabled encounter type
5. Review information blocking policies — results should not be unreasonably delayed

### Scenario: Proxy Cannot See Child's Records
1. Confirm proxy relationship is active in the patient's chart
2. Check adolescent breakaway rules — if teen is within breakaway age range, proxy access may be restricted by state law configuration
3. Verify relationship type is an authorized proxy type
4. Check if specific record types are restricted for the proxy (e.g., sensitive notes)

### Scenario: Patient Wants to Remove Proxy Access
1. Patient navigates to MyChart > Account Settings > Share My Record
2. Patient can revoke access directly, OR
3. Staff navigates to patient chart > MyChart > Proxies > Revoke
4. Document in patient chart per organizational policy

---

## Related Notes
- Epic-Session-Definitions-Limits
- Epic-Patient-Self-Service
- Epic-Provider-Masterfile
- Epic-Department-Build-Facility-Structure

## References
- Epic UserWeb — MyChart Build and Configuration Guide (requires login)
- Epic UserWeb — Adolescent Confidentiality Configuration
- Epic UserWeb — Information Blocking / 21st Century Cures Act Guide
- Open.epic.com — Patient Access resources: https://open.epic.com
- ONC 21st Century Cures Act Final Rule: https://www.healthit.gov/curesrule/

---
<- [[Epic-MOC]]