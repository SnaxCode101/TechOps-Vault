---
title: HL7 FHIR Fundamentals
type: reference
tags: [hl7, fhir, interoperability, api, healthcare-it, r4, intermediate]
section: 10-Healthcare-IT
created: 2026-03-27
difficulty: intermediate
---

# 🔗 HL7 FHIR Fundamentals

> **FHIR (Fast Healthcare Interoperability Resources)** is the modern standard for healthcare data exchange, developed by HL7 International. FHIR R4 is the current production standard and is mandated by CMS for patient access APIs.

---

## What Is FHIR?

FHIR is a RESTful API standard for exchanging healthcare information. Unlike older HL7 v2 (pipe-delimited messages), FHIR uses:
- **REST APIs** — standard HTTP verbs (GET, POST, PUT, DELETE)
- **JSON or XML** — human-readable formats
- **Resources** — modular data structures (Patient, Encounter, Observation, etc.)
- **SMART on FHIR** — OAuth2-based authorization framework for apps

---

## FHIR Versions

| Version | Status | Notes |
|---|---|---|
| DSTU1/2 | Deprecated | Early drafts; not used in production |
| STU3 | Legacy | Some legacy systems still on STU3 |
| R4 | **Current standard** | CMS mandate; most EHR implementations |
| R4B | Minor update | Specific resource updates |
| R5 | Published 2023 | Emerging; not yet widely deployed |

---

## Core FHIR Concepts

### Resources
Everything in FHIR is a **Resource** — a discrete, versioned, identifiable unit of health data.

| Resource | Description |
|---|---|
| **Patient** | Demographics, identifiers, contact info |
| **Practitioner** | Provider identity, credentials, NPI |
| **Encounter** | A clinical visit or interaction |
| **Observation** | Vitals, lab results, assessments |
| **Condition** | Diagnoses, problems |
| **MedicationRequest** | Prescriptions |
| **AllergyIntolerance** | Allergy records |
| **DiagnosticReport** | Lab/radiology reports |
| **DocumentReference** | CCD/CDA documents, scanned records |
| **Appointment** | Scheduled events |
| **Organization** | Hospital, clinic, department |
| **Location** | Physical locations |
| **Bundle** | Container for multiple resources |
| **OperationOutcome** | Error/status responses |

### FHIR REST API Pattern

```
Base URL: https://fhir.hospital.org/api/FHIR/R4/

GET    /Patient/{id}                    Read a patient
GET    /Patient?name=Smith&birthdate=1990  Search patients
POST   /Patient                         Create a patient
PUT    /Patient/{id}                    Update a patient
DELETE /Patient/{id}                    Delete (rare in healthcare)
GET    /Patient/{id}/_history           Version history
GET    /metadata                        CapabilityStatement (what server supports)
```

### HTTP Status Codes in FHIR

| Code | Meaning |
|---|---|
| 200 | Success |
| 201 | Created (POST) |
| 400 | Bad Request (invalid resource) |
| 401 | Unauthorized |
| 403 | Forbidden (authorized but not permitted) |
| 404 | Resource not found |
| 422 | Unprocessable Entity (validation failed) |
| 500 | Server error |

---

## SMART on FHIR (Authorization)

SMART on FHIR = **FHIR + OAuth2 + OpenID Connect**. It enables third-party apps to securely access EHR data.

### Auth Flow
```
1. App registers with EHR (client_id, redirect_uri, scopes)
2. User launches app (from EHR or standalone)
3. App redirects to EHR authorization server
4. User authenticates (EHR login)
5. EHR issues authorization code
6. App exchanges code for access_token (+ refresh_token)
7. App calls FHIR API with Bearer token
8. EHR validates token → returns requested resources
```

### SMART Scopes

| Scope | Access |
|---|---|
| `patient/Patient.read` | Read Patient resource for current patient |
| `user/Patient.read` | Read Patient resources user has access to |
| `system/Patient.read` | Backend service access (no user context) |
| `launch` | EHR launch context |
| `launch/patient` | Patient context included in launch |
| `openid profile` | Identity token (user info) |

---

## CMS Interoperability & Patient Access Rule (2020)

| Requirement | Detail |
|---|---|
| Patient Access API | Payers must expose FHIR R4 API for members to access claims, clinical data |
| Provider Directory API | Payers must expose provider network via FHIR |
| Payer-to-Payer exchange | Members can request data transfer when switching plans |
| Info Blocking prohibition | Covered entities cannot block access to EHI |
| USCDI | United States Core Data for Interoperability — minimum data set |

**Epic FHIR implementation:** Epic exposes FHIR R4 endpoints via App Orchard. External apps use SMART on FHIR for launch. Internal FHIR used for CCD exchange, patient-facing APIs (MyChart), and payer connectivity.

---

## FHIR Security Considerations

| Risk | Mitigation |
|---|---|
| Unauthenticated FHIR endpoints | Always require SMART/OAuth2; no open endpoints |
| Overly broad scopes | Scope minimization; `patient/*.read` vs `system/*.write` |
| Token exfiltration | Short-lived access tokens (1hr); refresh token rotation |
| FHIR bulk export abuse | `$export` operation can dump entire patient population — restrict to authorized systems |
| Audit logging | All FHIR API calls should be logged with user, resource, timestamp |
| PHI in URLs | FHIR search params can include PHI (name, DOB) — use POST-based search, enforce HTTPS |

### FHIR Bulk Data (`$export`)
```
GET /Patient/$export
→ Returns NDJSON files of all Patient resources
→ Highly sensitive — equivalent to a full database dump
→ Restrict to backend services with system/* scope
→ Requires audit trail and approval workflow
```

---

## CapabilityStatement

The FHIR CapabilityStatement (`/metadata`) declares what a server supports:
```json
{
  "resourceType": "CapabilityStatement",
  "fhirVersion": "4.0.1",
  "rest": [{
    "mode": "server",
    "resource": [{
      "type": "Patient",
      "interaction": [
        {"code": "read"},
        {"code": "search-type"}
      ]
    }]
  }]
}
```
Use this to enumerate supported resources and interactions when integrating with an unknown FHIR server.

---

## FHIR vs HL7 v2 vs CCD/CDA

| Feature | HL7 v2 | CDA/CCD | FHIR R4 |
|---|---|---|---|
| Format | Pipe-delimited text | XML | JSON / XML |
| Transport | MLLP (TCP) | HTTP / file | REST HTTP |
| Human readable | No | Partial | Yes |
| API-native | No | No | Yes |
| Modern use | Legacy integrations | C-CDA summaries | New integrations, apps |
| Maturity | Very high (30+ years) | High | High (R4 stable) |

---

## Related Notes
- Healthcare-IT-Overview
- HL7-v2-Messaging
- HIPAA-Technical-Safeguards
- Healthcare-IT-Integration-Patterns
- Epic-Overview
- Epic-Patient-Self-Service

---

## References
- HL7.org — FHIR R4 specification (fhir.hl7.org)
- SMART Health IT — SMART on FHIR documentation
- CMS.gov — Interoperability and Patient Access Final Rule
- ONC HealthIT.gov — USCDI v3
- Epic on FHIR — open.epic.com

---
<- [[Healthcare-IT-MOC]]