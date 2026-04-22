---
title: Healthcare IT Integration Patterns
type: reference
tags:
  - healthcare-it
  - integration
  - hl7
  - fhir
  - epic
  - servicenow
  - interfaces
  - intermediate
section: 10-Healthcare-IT
created: 2026-03-27
difficulty: intermediate
---

# 🔀 Healthcare IT Integration Patterns

> Healthcare environments involve dozens of interconnected systems. This note covers the common integration patterns, data flows, and design considerations for healthcare IT infrastructure.

---

## Why Integration Is Complex in Healthcare

- **Heterogeneous systems:** EHR (Epic), lab (Sunquest/Beaker), radiology (PACS/RIS), pharmacy (Pyxis), billing (Cerner Rev Cycle), scheduling, etc.
- **Legacy protocols:** HL7 v2/MLLP still dominant in real-time clinical messaging
- **No single patient identifier:** MRN varies by facility; National Patient Identifier doesn't exist in the US
- **Regulatory requirements:** HIPAA mandates audit trails on all ePHI in motion
- **High availability needs:** Clinical systems run 24/7; integration failures have patient safety implications

---

## Core Integration Patterns

### 1. Point-to-Point (Legacy)
```
System A ──── hard-coded interface ───► System B
```
- Simple but brittle; N×(N-1) connections at scale
- No centralized monitoring or transformation
- Common in older hospital environments

### 2. Hub-and-Spoke (Integration Engine)
```
System A ──►│              │──► System C
System B ──►│  Integration │──► System D
System E ──►│    Engine    │──► System F
```
- Central engine (Mirth, Rhapsody, Ensemble) handles routing + transformation
- Single point of management; easier to audit
- **Best practice for HL7 v2 environments**

### 3. Event-Driven (Modern)
```
System A ──► Event Bus / Message Queue ──► Subscriber A
                  (Kafka, Azure SB)    ──► Subscriber B
                                       ──► Subscriber C
```
- Decoupled producers and consumers
- Scalable; supports replay and audit
- Used with FHIR Subscriptions and modern EHR APIs

### 4. API Gateway (FHIR-native)
```
Client App ──► API Gateway ──► FHIR Server (Epic) ──► Clinical Data
              (Auth + Rate   (SMART on FHIR)
               Limiting)
```
- OAuth2/SMART for auth; REST for transport
- Centralized policy enforcement, logging, and rate limiting
- **Modern standard per CMS 2020 rule**

---

## Common Healthcare System Interfaces

| Interface | Protocol | Direction | Use |
|---|---|---|---|
| ADT feed (Epic → downstream) | HL7 v2 ADT | Outbound | Notify all systems of patient movements |
| Lab orders (Epic → Lab) | HL7 v2 ORM | Outbound | Send lab orders from EHR to LIS |
| Lab results (Lab → Epic) | HL7 v2 ORU | Inbound | Return results into EHR |
| Radiology orders/results | HL7 v2 ORM/ORU | Bidirectional | Imaging orders and reports |
| Scheduling (Epic → 3rd party) | HL7 v2 SIU or FHIR Appointment | Outbound | Appointment notification |
| Patient access (Patient → Epic) | FHIR R4 / SMART | Inbound | MyChart API, patient apps |
| Payer connectivity | HL7 FHIR, X12 EDI 837/835 | Bidirectional | Claims, eligibility, prior auth |
| Document exchange (C-CDA) | HL7 CDA/FHIR DocumentReference | Bidirectional | Care summaries, referrals |
| Medical devices | HL7 v2 ORU or proprietary | Inbound | Vitals, ventilator, monitor data |
| ServiceNow ↔ Epic | REST API / webhooks | Bidirectional | IT tickets tied to EHR outages/issues |

---

## Epic Integration Architecture

```
External Systems
      │
      ▼
 Interconnect (Epic's integration broker)
      │
      ├── Bridges (inbound HL7 v2 parsing)
      ├── Routing Rules
      ├── Translators (v2 → Epic Chronicle)
      └── Outbound queues (Epic → external)
```

**Epic-specific integration tools:**
- **Bridges** — Epic's HL7 v2 interface engine (built-in, not Mirth)
- **Interconnect** — Epic's web services layer (REST/SOAP); used for FHIR R4 API, MyChart API
- **Chronicles** — Epic's proprietary database (MUMPS-based); not SQL
- **Cache** — InterSystems Caché (the underlying database platform for Chronicles)

---

## ServiceNow + Epic Integration

See dedicated note: [[ServiceNow-Epic-Integration]]

**Key patterns:**
- Epic downtime notifications → ServiceNow incident auto-creation
- Change management: Epic upgrades tracked in ServiceNow CAB
- Application onboarding: Epic access requests via ServiceNow catalog
- CMDB: Epic servers, interfaces, and application records in ServiceNow

---

## X12 EDI — Claims & Eligibility

Healthcare financial transactions use **X12 EDI** (ANSI X12), not HL7:

| Transaction | Description |
|---|---|
| 270/271 | Eligibility inquiry / response |
| 276/277 | Claim status inquiry / response |
| 278 | Prior authorization request/response |
| 835 | Electronic Remittance Advice (ERA) — payment explanation |
| 837P | Professional claim (physician billing) |
| 837I | Institutional claim (hospital billing) |

---

## Integration Security Controls

| Control | Implementation |
|---|---|
| Encrypt HL7 v2 in transit | TLS wrapper over MLLP; VPN tunnel; no raw port 2575 exposure |
| Authenticate integration engine | Service accounts with MFA; certificate-based auth |
| Audit all interface traffic | Log source IP, message type, control ID, timestamp |
| Segment interfaces by environment | Prod, UAT, Dev interfaces must not cross-connect |
| Monitor for interface failures | Alert on AE/AR ACKs, queue depth, message volume anomalies |
| Validate inbound messages | Schema validation; reject malformed messages before processing |

---

## Troubleshooting Integration Issues

```
Interface not sending?
  1. Check integration engine queue depth
  2. Verify TCP connection to target (telnet HOST PORT)
  3. Check firewall rules (MLLP ports, HTTPS port 443)
  4. Review ACK responses — AE = processing error, AR = rejection
  5. Check HL7 message structure against spec

No data appearing in receiving system?
  1. Confirm message was ACK'd (AA) by integration engine
  2. Trace message through engine routing rules
  3. Check transformation logs for mapping errors
  4. Verify receiving system accepted (check its log/queue)
  5. Check for duplicate message suppression on receiver
```

---

## Related Notes
- Healthcare-IT-Overview
- HL7-FHIR-Fundamentals
- HL7-v2-Messaging
- HIPAA-Technical-Safeguards
- ServiceNow-Epic-Integration
- Epic-Overview

---

## References
- HL7.org — messaging standards
- X12.org — EDI transaction sets
- Epic UserWeb — Bridges and Interconnect documentation [VERIFY: Epic UserWeb access]
- Mirth Connect documentation
- Microsoft Azure Health Data Services documentation

---
<- [[Healthcare-IT-MOC]]