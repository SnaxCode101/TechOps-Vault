---
title: Epic — Session Definitions & Limits
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - sessions
  - timeout
  - security
  - mychart
  - hyperspace
  - configuration
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic security configuration guides
difficulty: intermediate
---

# ⏱️ Epic — Session Definitions & Limits

## Overview

Epic manages user sessions across multiple application surfaces: **Hyperspace** (clinical workstation), **MyChart** (patient portal), **Haiku/Canto** (mobile apps), and **Rover** (mobile nursing/pharmacy). Each surface has its own session configuration. Proper session management is critical for HIPAA compliance, reducing unauthorized access risk, and meeting organizational security policies.

> ⚠️ **Flag:** Session timeout values and property names differ between Epic versions and application types. Values noted here are typical industry defaults; verify all timeout settings against your organization's installed version in Epic UserWeb or Test environment before applying to production.

---

## Core Concepts

### Session Types in Epic

| Application | User Type | Session Description |
|---|---|---|
| **Hyperspace** | Clinical/Admin staff | Windows client or web-based workstation session |
| **MyChart Web** | Patients/Proxies | Browser-based patient portal session |
| **MyChart Mobile** | Patients/Proxies | iOS/Android app session |
| **Haiku** | Physicians (mobile) | iOS/Android clinical mobile session |
| **Canto** | Physicians (tablet) | iPad clinical mobile session |
| **Rover** | Nurses/Pharmacy (mobile) | iOS/Android bedside/pharmacy session |
| **Healthy Planet / Radar** | Population health / care management | Web-based analytics session |

---

## Hyperspace Session Configuration

### Session Timeout (Idle Lock)
- **Idle Lock** — Hyperspace screen locks after a period of inactivity (user remains logged in, screen is locked)
- **Auto-Logout** — Hyperspace terminates session entirely after extended inactivity

| Setting | Typical Default | Location |
|---|---|---|
| Idle lock time | 5–15 minutes | Workstation/computer policy or Epic environment settings |
| Auto-logout time | 30–60 minutes | Epic System Definitions |
| Screen lock message | Configurable text | System Definitions |

> **Note:** Idle lock in Hyperspace is often managed at the **Windows Group Policy / OS level** (screen saver + lock) rather than exclusively within Epic. Epic may also enforce its own inactivity timeout independently. Your organization likely has both layers.

### Concurrent Sessions
- By default, Epic Hyperspace allows a user to have **one active session per workstation** but may allow multiple browser tabs (web Hyperspace) depending on configuration
- Concurrent session policies are enforced through **Epic login configuration** and organizational IT policy
- If a user logs in from a second workstation, behavior depends on configuration: session on first workstation may be locked or terminated
- [VERIFY: Epic UserWeb — Concurrent Login Configuration]

### Hyperspace Login Security
| Feature | Description |
|---|---|
| Single Sign-On (SSO) | Most organizations integrate Epic with Active Directory / SAML-based SSO |
| Smart Card / CAC Login | Supported via Epic and Windows infrastructure |
| Proximity Card / Tap-to-sign | Supported (e.g., Imprivata, DigitalNet) for fast user switching at shared workstations |
| Fingerprint biometric | Supported through third-party integrations |
| Password expiration | Typically managed by Active Directory, not Epic directly |

### Fast User Switching (Shared Workstation)
Many clinical environments use **Imprivata** (or similar) for tap-to-login at shared workstations. Epic supports:
- **Fast User Switching** — quick badge tap to swap to another user's Hyperspace session
- **In-Room Login (IRL)** — Epic-native feature for quick context switching
- Sessions are **locked** (not logged out) when user taps away; another user can then tap in on the same workstation

---

## MyChart Session Configuration

### MyChart Web Session
| Setting | Typical Default | Notes |
|---|---|---|
| Idle session timeout | 15–20 minutes | Configurable in MyChart system properties |
| Absolute session timeout | 60–120 minutes | Max session length regardless of activity |
| Session warning dialog | Shown ~2 minutes before timeout | Warns patient before auto-logout |
| Remember me / Stay logged in | On/Off toggle | Organizational security policy decision |
| Re-authentication for sensitive actions | Required | E.g., changing password, updating payment info |

### MyChart Mobile Session
| Setting | Typical Default | Notes |
|---|---|---|
| App session persistence | Configurable | App may stay "open" but re-authenticates after idle period |
| Biometric authentication | Supported (Face ID, Touch ID) | Patient convenience feature |
| Device trust | Configurable — trusted device may skip 2FA for X days | |
| Remote session revoke | Admin can revoke all sessions from Account Management | |

### Session Limits — MyChart Concurrent Access
- Patients can generally access MyChart from multiple devices simultaneously (not restricted to one session)
- Proxy accounts: no specific concurrent session limits documented as a default, but organizations can configure restrictions
- [VERIFY: Epic UserWeb — MyChart concurrent session limits]

---

## Session Definitions in Epic Scheduling (Visit Sessions)

> **Important:** The word "session" has a **different meaning in Epic Scheduling** — it refers to a **scheduling session block** (time period on a provider's template), not a user login session.

### Scheduling Session = Template Block of Time
In Epic scheduling, a **session** is a defined period of appointment availability on a provider's or department's schedule template. Sessions define:
- The **time block** (e.g., 8:00 AM – 12:00 PM)
- The **visit types** allowed in that time block
- The **number of slots** available
- The **location/department** the session is for

### Session Limits in Scheduling Context
| Term | Meaning |
|---|---|
| **Session** | A block of template time (e.g., Monday AM clinic, 8:00–12:00) |
| **Slot** | A single appointment opening within a session |
| **Session limit** | Max appointments bookable in a session (controlled by template) |
| **Overbooking limit** | Number of appointments that can be scheduled beyond template capacity |
| **Hold slots** | Slots held for specific visit types, not available to general scheduling |

### Visit Type Limits
- Each **EAP (Visit Type)** record has duration, associated departments, and scheduling rules
- Visit type limits control who can schedule (staff-only, patient-facing, both)
- Online scheduling (MyChart) can be enabled/disabled per visit type
- See [[Epic-Pools-Blocks-Modifiers]] for pool and block configuration

---

## HIPAA & Compliance Relevance

| Requirement | Epic Feature |
|---|---|
| Automatic logoff (HIPAA §164.312(a)(2)(iii)) | Hyperspace idle lock + auto-logout |
| Audit controls (HIPAA §164.312(b)) | Epic audit trails log all session events |
| Unique user identification (HIPAA §164.312(a)(2)(i)) | Per-user logins; shared accounts not compliant |
| Emergency access procedure (HIPAA §164.312(a)(2)(ii)) | Epic Break-the-Glass (emergency override access) |

### Break-the-Glass (Emergency Override)
- Epic's **Break-the-Glass** allows a user to access a chart they are not otherwise authorized to view (e.g., patient is a family member of a staff user)
- Access is **logged and audited automatically**
- User must provide a reason code
- Security/privacy team is often automatically notified
- This is an audit/access control feature, not a session feature, but is related to session-level access control

---

## Common Scenarios & Job-Aid

### Scenario: User Getting Logged Out Too Quickly
1. Determine if timeout is from Windows GPO (screen saver lock) or Epic itself
2. Check with IT for workstation-level policies
3. For Hyperspace-specific timeout: review Epic system definition for idle lock settings
4. Clinical areas with patient safety needs (e.g., ICU, OR) may have extended timeout exceptions — document and approve per security policy

### Scenario: Patient Reports Being Logged Out of MyChart Unexpectedly
1. Confirm whether it is idle timeout or absolute timeout triggering
2. Check if patient is accessing via older browser (session management issues)
3. Review if 2FA is causing re-authentication friction
4. Check for MyChart maintenance windows that may have terminated active sessions

### Scenario: Need to Force-Terminate a Specific User's Session
- Hyperspace: contact Epic system admin or use Epic's session management tools to force session termination
- MyChart: navigate to **MyChart Admin > Account Management > Active Sessions** (if enabled for your version) and revoke
- For suspected security incident: lock account immediately, document, escalate to security team

---

## Related Notes
- Epic-MyChart-Rules
- Epic-Pools-Blocks-Modifiers
- Epic-Provider-Masterfile

## References
- Epic UserWeb — MyChart Security and Session Configuration
- Epic UserWeb — Hyperspace Login and Session Management
- HIPAA Security Rule §164.312 — Technical Safeguards
- Imprivata: https://www.imprivata.com (third-party SSO/fast user switching)

---
<- [[Epic-MOC]]