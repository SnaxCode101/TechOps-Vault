---
title: Learning Path — SOC Analyst
tags:
  - MOC
  - learning-path
  - SOC
  - blue-team
  - foundational
  - intermediate
topic: Learning Paths
difficulty: foundational
created: 2026-04-03
updated: 2026-04-03
source: SecOps Vault — Session 9
---

# Learning Path — SOC Analyst

## Overview
This path builds the core knowledge required for a Tier 1–2 SOC Analyst role. It follows a foundational-to-intermediate blue-team track covering networking, defensive concepts, threat detection, and incident response. Estimated total time: **80–110 hours**.

Target roles: SOC Analyst (Tier 1/2), Security Analyst, Incident Responder, Blue Team Analyst.

---

## Phase 1 — Foundations (Est. 20–25 hrs)

Build the baseline technical knowledge every SOC analyst needs before touching any tool.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 1 | [[CIA-Triad]] | Foundational | 1 hr | None |
| 2 | [[Networking-Basics]] | Foundational | 3 hrs | None |
| 3 | [[OSI-Model]] | Foundational | 2 hrs | Networking-Basics |
| 4 | [[TCP-IP-Fundamentals]] | Foundational | 2 hrs | OSI-Model |
| 5 | [[Cryptography-Basics]] | Foundational | 2 hrs | CIA-Triad |
| 6 | [[Common-Ports]] | Foundational | 1 hr | TCP-IP-Fundamentals |
| 7 | [[Social-Engineering]] | Foundational | 1 hr | CIA-Triad |
| 8 | [[OWASP-Top-10]] | Intermediate | 3 hrs | Networking-Basics |
| 9 | [[Linux-Commands]] | Foundational | 2 hrs | None |
| 10 | [[Windows-Commands]] | Foundational | 2 hrs | None |

> [!tip] Phase 1 Checkpoint
> You should be able to: explain the CIA triad, describe how TCP/IP works, name common ports and what runs on them, identify the layers of the OSI model, and understand basic cryptographic concepts before moving to Phase 2.

---

## Phase 2 — Blue Team Core (Est. 30–35 hrs)

Learn how defenders operate — detection, analysis, and response.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 11 | [[Threat-Intelligence]] | Intermediate | 3 hrs | Phase 1 complete |
| 12 | [[Vulnerability-Management]] | Intermediate | 2 hrs | Threat-Intelligence |
| 13 | [[Network-Defense-Blue-Team]] | Intermediate | 3 hrs | TCP-IP-Fundamentals |
| 14 | [[Incident-Response]] | Intermediate | 3 hrs | Network-Defense-Blue-Team |
| 15 | [[Malware-Analysis]] | Advanced | 4 hrs | Incident-Response |
| 16 | [[Digital-Forensics]] | Advanced | 4 hrs | Malware-Analysis |
| 17 | [[Wireshark]] | Intermediate | 3 hrs | TCP-IP-Fundamentals |
| 18 | [[Nmap]] | Foundational | 2 hrs | Networking-Basics |
| 19 | [[OpenVAS-Nessus]] | Intermediate | 2 hrs | Vulnerability-Management |
| 20 | [[Volatility]] | Advanced | 3 hrs | Malware-Analysis |
| 21 | [[Autopsy]] | Advanced | 3 hrs | Digital-Forensics |

> [!tip] Phase 2 Checkpoint
> You should be able to: analyze a pcap in Wireshark, run a basic Nmap scan, explain the incident response lifecycle, describe common malware behaviors, and understand how threat intelligence feeds into detection.

---

## Phase 3 — Active Directory and Windows Threats (Est. 15–20 hrs)

The majority of enterprise environments run Windows/AD. This phase covers the attack surface SOC analysts must monitor.

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 22 | [[Active-Directory]] | Intermediate | 4 hrs | Phase 1 complete |
| 23 | [[Windows-Privilege-Escalation]] | Advanced | 3 hrs | Active-Directory |
| 24 | [[AD-Attack-Reference]] | Intermediate | 2 hrs | Active-Directory |
| 25 | [[PowerShell-Security]] | Intermediate | 2 hrs | Windows-Commands |
| 26 | [[BloodHound]] | Advanced | 2 hrs | Active-Directory |
| 27 | [[Responder]] | Advanced | 2 hrs | AD-Attack-Reference |

> [!important] Why AD Matters for SOC
> BloodHound, Responder, and AD privilege escalation techniques are the most common attack vectors in enterprise breaches. A SOC analyst who cannot recognize these attack patterns in logs will miss the majority of real-world incidents.

---

## Phase 4 — Cloud and Modern Infrastructure (Est. 10–15 hrs)

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 28 | [[Cloud-Security-Fundamentals]] | Intermediate | 3 hrs | Networking-Basics |
| 29 | [[Container-Docker-Security]] | Intermediate | 2 hrs | Cloud-Security-Fundamentals |
| 30 | [[Vulnerability-Research-Reference]] | Intermediate | 2 hrs | Vulnerability-Management |
| 31 | [[Encoding-Decoding-Reference]] | Foundational | 1 hr | None |

---

## Recommended Certifications (In Order)

| Cert | Vault Note | Priority |
|------|-----------|---------|
| CompTIA Security+ | [[Security-Plus-SY0-701]] | First — industry baseline |
| CompTIA CySA+ | [[CySA-Plus-CS0-003]] | Second — SOC-specific |
| eJPT (INE) | [[eJPT-INE]] | Optional — builds attacker perspective |

---

## Recommended Labs (Blue Team Focus)

| Lab | Vault Note | Skills Covered |
|-----|-----------|----------------|
| THM Blue | [[THM-Blue]] | EternalBlue, Metasploit, post-exploitation |
| THM Ice | [[THM-Ice]] | Windows exploitation, privilege escalation |
| Custom Nmap Lab | [[Custom-Lab-Nmap-Enumeration]] | Host discovery, port scanning |

---

## See Also
- [[Learning-Path-Penetration-Tester]]
- [[Learning-Path-ServiceNow-Security-Admin]]
- [[TechOps-MOC]]
- [[Dashboard]]
