---
title: Learning Log — 2026-03-26
date: 2026-03-26
tags: |-
  - learning-log
  - session-3
  - audit
  - expansion
  - vault-build
session: 3
---

# 📓 Learning Log — 2026-03-26 (Session 3)

## Session Summary

**Duration:** Full session  
**Platform:** SecOps-Vault build session (Claude + Obsidian Local REST API)  
**Goal:** Full audit + complete vault expansion to production-ready state

---

## What Was Built Today

### Audit Findings
- Vault confirmed at 41 notes before session start
- Search tool referenced outdated note count (35 instead of 41) and old HTML — rebuilt
- 04-CTF, 07-Projects completely empty — built out
- 05-Certs missing 4 critical certs — added
- 01-Fundamentals missing 8 key domains — added
- 02-Tools missing 8 key tools — added
- 06-Reference missing 6 critical reference notes — added

### New Notes Added (33 total)

**01-Fundamentals (8 new):**
- Malware Analysis — static/dynamic, YARA, IOCs, sandbox workflow
- Digital Forensics — DFIR, disk imaging, memory forensics, Volatility, log analysis
- Cloud Security Fundamentals — AWS/Azure/GCP, IAM, S3, shared responsibility, attack techniques
- Social Engineering — phishing, spear-phishing, vishing, OSINT, SPF/DKIM/DMARC, GoPhish
- Threat Intelligence — CTI, MITRE ATT&CK, Pyramid of Pain, STIX/TAXII, feeds
- Vulnerability Management — CVE, CVSS, EPSS, CISA KEV, remediation workflows
- Network Defense & Blue Team — IDS/IPS, SIEM, EDR, Sysmon, threat hunting, SOC triage
- Reverse Engineering Basics — x86/x64 assembly, Ghidra, debuggers, anti-analysis

**02-Tools (8 new):**
- Impacket — secretsdump, psexec, wmiexec, GetNPUsers, GetUserSPNs, ticketer
- CrackMapExec — SMB/WinRM/LDAP, spray, dump, modules, pass-the-hash
- Volatility — memory forensics, pslist, malfind, netstat, hashdump, cmdline
- Nikto — web scanner, headers, misconfigs, default content
- Responder — LLMNR/NBT-NS poisoning, NTLMv2 capture, relay with ntlmrelayx
- Evil-WinRM — WinRM shell, pass-the-hash, file upload/download, AMSI bypass
- Autopsy — disk forensics, timeline, keyword search, registry, web artifacts
- OpenVAS / Nessus — GVM setup, scan types, CVSS interpretation, authenticated scanning

**04-CTF (2 new):**
- CTF Methodology — web, crypto, forensics, RE, pwn, OSINT, steganography checklists
- CTF Quick Reference — encoding table, magic bytes, hash identification, Python one-liners, default creds

**05-Certs (4 new):**
- eJPT (INE) — domains, core commands, pivoting, exam tips
- OSCP PEN-200 — full syllabus, buffer overflow skeleton, PrivEsc checklists, exam strategy
- CySA+ CS0-003 — 4 domains, SIEM queries, IR lifecycle, compliance frameworks
- CEH v12 — 20 modules, key techniques per module, exam strategy

**06-Reference (6 new):**
- Reverse Shell Cheatsheet — bash, nc, python, php, perl, ruby, PowerShell, msfvenom, TTY upgrade
- PowerShell Security — AMSI, CLM, execution policy, script block logging, download cradles, AD recon
- AD Attack Reference — full chain from recon → DCSync → golden ticket → persistence
- Regex Reference — syntax, quantifiers, security patterns, grep/sed/awk, Python re
- Encoding & Decoding Reference — base64, hex, URL, HTML, ROT13, XOR, JWT, CyberChef
- Vulnerability Research Reference — CVE workflow, searchsploit, CVSS scoring, notable CVEs, responsible disclosure

**07-Projects (2 new):**
- Home Lab Setup — hypervisors, VM list, network config, AD lab build, DVWA, safety rules
- Project Template — full pentest project documentation template

**00-MOC (updated):**
- Vault-Search.md — rebuilt as full keyword master index (74 notes)
- SecOps-Vault-Search.html — rebuilt with all 74 notes, folder filters, clickable cards

---

## Vault Final State: 74 Notes

| Section | Before | After |
|---|---|---|
| 01-Fundamentals | 12 | 20 |
| 02-Tools | 12 | 20 |
| 03-Labs | 3 | 3 |
| 04-CTF | 0 | 2 |
| 05-Certs | 3 | 7 |
| 06-Reference | 8 | 14 |
| 07-Projects | 0 | 2 |
| 08-Learning-Log | 3 | 4 |
| **Total** | **41** | **74** |

---

## Key Concepts Reinforced Today

- Full DFIR workflow: order of volatility, chain of custody, disk imaging, memory forensics
- MITRE ATT&CK tactics and enterprise matrix (14 tactics)
- Complete AD attack chain: enumeration → credential attacks → lateral movement → DCSync → persistence
- CTF approach: systematic category-based methodology, never start blind
- Buffer overflow skeleton (OSCP-style) — fuzzing → offset → bad chars → JMP ESP → shellcode
- CVSS + EPSS combination for vulnerability prioritization
- AMSI and PowerShell security controls (attack + defense perspective)

---

## Next Session Options

| Priority | Task |
|---|---|
| High | Add lab write-ups: HTB / THM boxes to 03-Labs/ |
| High | Add CTF write-ups to 04-CTF/ as you complete challenges |
| Medium | Add today's Learning Log entry to Learning-Log-Index |
| Medium | Build Impacket / CME lab write-up (use Project-Template) |
| Low | Add OSCP-style buffer overflow lab write-up to 03-Labs/ |
| Low | Add more tool notes: Ligolo-ng, Chisel, pwntools, Burp extensions |

---
*Log updated: 2026-03-26*

---
<- [[Learning-Log-MOC]]