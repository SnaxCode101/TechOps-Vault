---
title: Learning Log — 2026-03-28 — Session 6
tags: |-
  - learning-log
  - session-6
date: 2026-03-28
session: 6
vault_count: 148
---

# Learning Log — Session 6 (2026-03-28)

## Session Focus
CTF expansion, medium-difficulty lab write-ups, and full deep audit of sysadmin-relevant content with P1 rewrites.

---

## Work Completed

### Quick Fixes
- Dashboard.md — 138→139 counts, added Learning Log S5 link
- Vault-Search.md — Healthcare IT 6→7, TEFCA row added, footer 137→139

### Phase 1: CTF Expansion (+4 notes)
- CTF-Web-Exploitation.md — SQLi, XSS, SSTI, LFI/RFI, SSRF, deserialization, JWT, command injection
- CTF-Crypto-Attacks.md — Classical ciphers, 7 RSA attack types, XOR, block cipher attacks, hash attacks
- CTF-Forensics-Techniques.md — PCAP/Wireshark, Volatility 2+3, disk forensics, steganography deep dive
- CTF-Binary-Exploitation.md — Buffer overflow, ROP, format strings, heap, shellcode, pwntools

### Phase 2: Medium Labs (+5 write-ups)
- HTB-Optimum — Rejetto HFS CVE-2014-6287 → MS16-032
- HTB-Bastard — Drupalgeddon2 CVE-2018-7600 → JuicyPotato/MS15-051
- HTB-Devel — Anonymous FTP → ASPX webshell → MS11-046
- THM-Steel-Mountain — Rejetto HFS → Unquoted service path (PowerUp)
- THM-Relevant — SMB write → ASPX webshell → PrintSpoofer

### Phase 3: Full Deep Audit — Sysadmin Content
Read all sysadmin-relevant notes across sections. Classified difficulty levels and identified gaps.

**P1 Rewrites Completed:**
- Linux-Commands.md — Added: systemctl/journalctl, user/group mgmt, disk/storage, package mgmt, cron, SSH admin, firewall (ufw/firewalld/iptables), NFS/SMB mounts, log rotation. Tagged foundational.
- Windows-Commands.md — Added: Get-WinEvent + Event ID table, AD admin commands, GPO management, Remote PS (Enter-PSSession/Invoke-Command), DISM/SFC, Windows Update, disk management, backup (wbadmin). Tagged foundational.
- Active-Directory.md — Major rewrite: Added full admin sections (user/group/computer mgmt, GPO administration, DNS integration, replication/health, backup/restore, LAPS, Sites & Services, AD CS, hardening checklist). Kept all attack content. Tagged intermediate.

---

## Vault State: 148 notes (unchanged — rewrites, no new notes in Phase 3)

## Difficulty Classification Started
- foundational: Linux-Commands, Windows-Commands, Common-Ports
- intermediate: Active-Directory, Incident-Response, Network-Defense, Cloud-Security, PowerShell-Security
- Classification for remaining ~140 notes: deferred to Session 7

---

## Deferred to Session 7

| Priority | Task |
|---|---|
| High | Batch classify remaining notes (frontmatter difficulty + tags) |
| High | Networking-Basics.md — add practical admin sections (DNS zone mgmt, DHCP, VLAN, troubleshooting) |
| Medium | System-Admin-Reference.md — expand DNS/DHCP operational, cert mgmt, patch workflow |
| Medium | Bash-Scripting.md — add sysadmin automation scripts (backup, monitoring, user onboarding) |
| Medium | Common-Ports.md — add healthcare ports (HL7, DICOM) |
| Medium | Incident-Response.md — add ServiceNow IR integration, HIPAA breach notification |
| Low | Build learning pathway notes (structured study plans) |
| Low | Expand 07-Projects/ |
| Low | CSS snippet for Obsidian color-coding by difficulty |

---

## Key Patterns Noted Across Labs
- SeImpersonatePrivilege → token impersonation is the #1 Windows priv esc path for service accounts
- File upload to webroot (FTP or SMB) remains a top foothold technique
- CVE-2014-6287 (Rejetto HFS) appears in multiple boxes — common CTF/lab target
- Unpatched Windows = kernel exploit playground (MS11-046, MS15-051, MS16-032)

---

*Session 6 complete — 2026-03-28*
---
<- [[Learning-Log-MOC]]