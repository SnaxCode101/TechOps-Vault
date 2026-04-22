---
title: Learning Path — Healthcare IT / Epic Security
tags:
  - MOC
  - learning-path
  - healthcare-IT
  - Epic
  - medical-devices
  - HIPAA
  - foundational
  - intermediate
topic: Learning Paths
difficulty: foundational
created: 2026-04-03
updated: 2026-04-03
source: SecOps Vault — Session 9
---

# Learning Path — Healthcare IT / Epic Security

## Overview
This path builds the specialized knowledge required for security and IT roles in healthcare environments — covering HIPAA compliance, HL7/FHIR interoperability, Epic EHR administration, medical device security, and ServiceNow integration in clinical settings. Estimated total time: **80–110 hours**.

Target roles: Healthcare IT Security Analyst, Epic Security Admin, Clinical Systems Admin, Healthcare CISO/Analyst, Medical Device Security Engineer.

> [!important] Who This Path Is For
> This is a ==specialized convergence path== that combines cybersecurity fundamentals with healthcare-specific knowledge. It is distinct from general IT security — understanding ==HIPAA technical safeguards==, ==HL7 messaging==, and ==Epic's security model== is required knowledge that does not appear in standard security certifications.

---

## Phase 1 — Security and IT Foundations (Est. 15–20 hrs)

These notes provide the technical baseline required before healthcare-specific content.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 1 | [[CIA-Triad]] | Foundational | 1 hr | None |
| 2 | [[Networking-Basics]] | Foundational | 3 hrs | None |
| 3 | [[TCP-IP-Fundamentals]] | Foundational | 2 hrs | Networking-Basics |
| 4 | [[Cryptography-Basics]] | Foundational | 2 hrs | CIA-Triad |
| 5 | [[Vulnerability-Management]] | Intermediate | 2 hrs | CIA-Triad |
| 6 | [[Incident-Response]] | Intermediate | 3 hrs | Vulnerability-Management |
| 7 | [[System-Admin-Reference]] | Intermediate | 2 hrs | Networking-Basics |

> [!tip] Phase 1 Checkpoint
> You should understand CIA triad, basic networking, encryption fundamentals, and the incident response lifecycle before moving to healthcare-specific content.

---

## Phase 2 — Healthcare IT Core (Est. 20–25 hrs)

The foundational healthcare-specific knowledge layer. These notes cover concepts unique to clinical environments.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 8 | [[Healthcare-IT-Overview]] | Foundational | 2 hrs | Phase 1 complete |
| 9 | [[HIPAA-Technical-Safeguards]] | Intermediate | 3 hrs | Healthcare-IT-Overview |
| 10 | [[HL7-v2-Messaging]] | Intermediate | 3 hrs | Healthcare-IT-Overview |
| 11 | [[HL7-FHIR-Fundamentals]] | Intermediate | 3 hrs | HL7-v2-Messaging |
| 12 | [[Healthcare-IT-Integration-Patterns]] | Advanced | 4 hrs | HL7-FHIR-Fundamentals |
| 13 | [[TEFCA-Care-Coordination]] | Advanced | 2 hrs | HL7-FHIR-Fundamentals |

> [!important] HIPAA Technical Safeguards
> ==HIPAA Technical Safeguards== are the most-tested healthcare security concept in interviews and certifications. Focus on the four required implementation specifications: ==access controls==, ==audit controls==, ==integrity controls==, and ==transmission security==.
> See [[HIPAA-Technical-Safeguards]]

---

## Phase 3 — Epic EHR Administration (Est. 20–25 hrs)

Epic is the dominant EHR platform in US healthcare. Understanding its security model, user administration, and workflow configuration is essential for clinical IT roles.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 14 | [[Epic-Overview]] | Foundational | 2 hrs | Phase 2 complete |
| 15 | [[Epic-Clinic-Workflow]] | Intermediate | 2 hrs | Epic-Overview |
| 16 | [[Epic-Provider-Masterfile]] | Intermediate | 2 hrs | Epic-Overview |
| 17 | [[Epic-Department-Build-Facility-Structure]] | Advanced | 3 hrs | Epic-Provider-Masterfile |
| 18 | [[Epic-Session-Definitions-Limits]] | Intermediate | 2 hrs | Epic-Department-Build-Facility-Structure |
| 19 | [[Epic-Pools-Blocks-Modifiers]] | Advanced | 2 hrs | Epic-Session-Definitions-Limits |
| 20 | [[Epic-MyChart-Rules]] | Intermediate | 2 hrs | Epic-Overview |
| 21 | [[Epic-Patient-Self-Service]] | Foundational | 1 hr | Epic-MyChart-Rules |
| 22 | [[Epic-Content-Management]] | Intermediate | 2 hrs | Epic-Overview |
| 23 | [[Epic-SmartText-SmartPhrases]] | Foundational | 1 hr | Epic-Content-Management |
| 24 | [[Epic-Questionnaire-Build]] | Intermediate | 2 hrs | Epic-Content-Management |
| 25 | [[Epic-Reporting-Workbench-Slicer-Dicer]] | Intermediate | 2 hrs | Epic-Overview |

> [!tip] Phase 3 Checkpoint
> You should be able to: describe Epic's organizational structure (department → facility → enterprise), explain how provider accounts are configured, understand MyChart security settings, and navigate Epic's basic build hierarchy.

---

## Phase 4 — Medical Device Security (Est. 15–20 hrs)

Medical device security is a rapidly growing specialty. These notes cover the regulatory, network, and incident response aspects unique to clinical devices.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 26 | [[Medical-Devices-Overview]] | Foundational | 2 hrs | Phase 2 complete |
| 27 | [[IoT-OT-Security-Fundamentals]] | Intermediate | 3 hrs | Medical-Devices-Overview |
| 28 | [[Medical-Device-Network-Security]] | Advanced | 3 hrs | IoT-OT-Security-Fundamentals |
| 29 | [[Medical-Device-Risk-Management]] | Advanced | 3 hrs | Medical-Device-Network-Security |
| 30 | [[FDA-Cybersecurity-Guidance]] | Intermediate | 2 hrs | Medical-Device-Risk-Management |
| 31 | [[Medical-Device-Incident-Response]] | Advanced | 3 hrs | Incident-Response + Medical-Device-Network-Security |

> [!important] Medical Device Security Key Concepts
> - ==FDA Cybersecurity Guidance:== Manufacturers must address cybersecurity in pre-market submissions; post-market patch management required
> - ==Network segmentation:== Medical devices should be on isolated VLANs; never on the same segment as general workstations
> - ==Legacy devices:== Many medical devices run EOL Windows; patching is constrained by FDA clearance; compensating controls are the primary defense
> - ==Incident response:== Patient safety is the first priority; clinical staff must be consulted before isolating any device that may be in active use

---

## Phase 5 — ServiceNow + Healthcare Integration (Est. 10 hrs)

For roles that use ServiceNow in clinical settings — Epic integration, ITSM for clinical workflows, and security operations.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 32 | [[ServiceNow-Epic-Integration]] | Advanced | 4 hrs | Epic-Overview + ServiceNow-CSA |
| 33 | [[ServiceNow-CIS-SIR]] | Intermediate | 3 hrs | ServiceNow-CSA |
| 34 | [[ServiceNow-CIS-VR]] | Intermediate | 3 hrs | CIS-SIR |

---

## Recommended Certifications (In Order)

| Cert | Vault Note | Priority |
|------|-----------|---------|
| CompTIA Security+ | [[Security-Plus-SY0-701]] | First — baseline required |
| CompTIA A+ | [[CompTIA-Aplus-Core1-220-1201]] | Optional — strong for helpdesk to analyst transition |
| ServiceNow CSA | [[ServiceNow-CSA]] | High value in ServiceNow healthcare shops |
| CySA+ | [[CySA-Plus-CS0-003]] | Strong for healthcare security analyst roles |

---

## Key Cross-Section Connections

| Healthcare Concept | Related Security Concept | Vault Link |
|-------------------|-------------------------|-----------|
| HIPAA breach notification | Incident Response | [[Incident-Response]] |
| PHI data classification | CIA Triad | [[CIA-Triad]] |
| FHIR API security | Web Application Testing | [[Web-Application-Testing]] |
| Medical device network | Network Defense | [[Network-Defense-Blue-Team]] |
| HL7 message integrity | Cryptography Basics | [[Cryptography-Basics]] |
| Epic audit logs | Threat Intelligence | [[Threat-Intelligence]] |

---

## See Also
- [[Learning-Path-SOC-Analyst]]
- [[Learning-Path-ServiceNow-Security-Admin]]
- [[TechOps-MOC]]
- [[Dashboard]]
