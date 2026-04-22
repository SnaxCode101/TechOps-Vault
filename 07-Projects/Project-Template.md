---
title: Security Project Template
tags: |-
  - project
  - template
  - scope
  - methodology
  - findings
  - report
topic: Projects
created: 2026-03-26
updated: 2026-03-26
---

# 🗂️ Security Project Template

> Copy this note and rename it for each new project. Fill in each section as you work.

---

## Project Overview

| Field | Value |
|---|---|
| Project Name | |
| Type | Penetration Test / Security Assessment / CTF / Research / Lab |
| Target | |
| Start Date | |
| End Date | |
| Status | Planning / Active / Complete |
| Platform | HTB / THM / VulnHub / Internal Lab / Client / Other |

---

## Scope & Objectives

### In Scope
```
- 
- 
```

### Out of Scope
```
- 
- 
```

### Objectives
1. 
2. 
3. 

### Rules of Engagement
- Authorized by: 
- Testing hours: 
- Prohibited actions: 
- Emergency contact: 

---

## Tools & Setup

| Tool | Version | Purpose |
|---|---|---|
| | | |

```bash
# Network setup
ip a
ping -c3 TARGET_IP
sudo openvpn lab.ovpn   # If applicable
```

---

## Reconnaissance

### Passive Recon
```
OSINT findings:
- 
```

### Active Recon
```bash
# Nmap results
nmap -sC -sV -p- -oA TARGET_NAME TARGET_IP

# Open ports and services:
# PORT   SERVICE   VERSION
# 
```

### Web Enumeration
```bash
gobuster dir -u http://TARGET_IP -w /usr/share/seclists/Discovery/Web-Content/common.txt -x php,html,txt -o gobuster.txt

# Directories found:
#
```

---

## Vulnerabilities Found

### Finding 1 — [Title]

| Field | Value |
|---|---|
| Severity | Critical / High / Medium / Low / Info |
| CVE | CVE-YYYY-NNNNN (if applicable) |
| CVSS Score | |
| Affected Component | |
| Tool | |

**Description:**
[What the vulnerability is]

**Proof of Concept:**
```bash
# Steps to reproduce:
```

**Evidence:**
```
[Output / screenshot description]
```

**Remediation:**
[How to fix it]

---

### Finding 2 — [Title]
*(duplicate above section as needed)*

---

## Exploitation

### Initial Access
```bash
# Commands used for initial foothold:
```

### Privilege Escalation
```bash
# PrivEsc path:
```

### Post-Exploitation
```bash
# Lateral movement / persistence / data access:
```

### Flags / Objectives Achieved
```
user.txt:  
root.txt:  
Objective: 
```

---

## Attack Chain Summary

```
[External Recon] 
  → [Service Enumeration: port X running Y vZ]
  → [Exploit: CVE-YYYY-NNNNN]
  → [Initial Shell: user@host]
  → [PrivEsc: method]
  → [Root/SYSTEM: proof.txt]
```

---

## Lessons Learned

**What worked:**
- 

**What didn't work / rabbit holes:**
- 

**New techniques learned:**
- 

**What to do differently next time:**
- 

---

## References

- CVE links: 
- Tool documentation: 
- Writeup references: 

---

## Recommendations (for client/self)

| Priority | Finding | Remediation | Effort |
|---|---|---|---|
| Critical | | | |
| High | | | |
| Medium | | | |

---
*Template version: 1.0 | Created: 2026-03-26*

---
<- [[Projects-MOC]]