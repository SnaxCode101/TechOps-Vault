---
title: Learning Path — Penetration Tester
tags:
  - MOC
  - learning-path
  - penetration-testing
  - red-team
  - offensive
  - intermediate
  - advanced
topic: Learning Paths
difficulty: intermediate
created: 2026-04-03
updated: 2026-04-03
source: SecOps Vault — Session 9
---

# Learning Path — Penetration Tester

## Overview
This path builds the knowledge and hands-on skills required for a junior-to-mid penetration tester. It follows a foundational-to-advanced offensive track covering enumeration, exploitation, post-exploitation, and reporting. Estimated total time: **120–160 hours**.

Target roles: Junior Penetration Tester, Security Consultant, Offensive Security Analyst, Bug Bounty Hunter.

> [!warning] Prerequisites
> This path assumes basic networking and Linux knowledge. Complete **Phase 1 of the SOC Analyst path** before starting here, or work through Phase 1 below in parallel.

---

## Phase 1 — Technical Foundations (Est. 20–25 hrs)

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 1 | [[Networking-Basics]] | Foundational | 3 hrs | None |
| 2 | [[TCP-IP-Fundamentals]] | Foundational | 2 hrs | Networking-Basics |
| 3 | [[OSI-Model]] | Foundational | 2 hrs | Networking-Basics |
| 4 | [[Linux-Commands]] | Foundational | 2 hrs | None |
| 5 | [[Common-Ports]] | Foundational | 1 hr | TCP-IP-Fundamentals |
| 6 | [[Bash-Scripting]] | Foundational | 2 hrs | Linux-Commands |
| 7 | [[Python-for-Security]] | Intermediate | 3 hrs | Bash-Scripting |
| 8 | [[Cryptography-Basics]] | Foundational | 2 hrs | None |
| 9 | [[Encoding-Decoding-Reference]] | Foundational | 1 hr | Cryptography-Basics |

---

## Phase 2 — Penetration Testing Methodology (Est. 15–20 hrs)

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 10 | [[Penetration-Testing-Methodology]] | Intermediate | 3 hrs | Phase 1 complete |
| 11 | [[OSINT-Methodology]] | Intermediate | 2 hrs | Penetration-Testing-Methodology |
| 12 | [[Nmap]] | Foundational | 2 hrs | TCP-IP-Fundamentals |
| 13 | [[Gobuster]] | Foundational | 1 hr | Penetration-Testing-Methodology |
| 14 | [[Nikto]] | Foundational | 1 hr | Penetration-Testing-Methodology |
| 15 | [[ffuf]] | Intermediate | 2 hrs | Gobuster |
| 16 | [[OWASP-Top-10]] | Intermediate | 3 hrs | Penetration-Testing-Methodology |
| 17 | [[Web-Application-Testing]] | Intermediate | 3 hrs | OWASP-Top-10 |

> [!tip] Phase 2 Checkpoint
> You should be able to: describe all phases of a pentest engagement, perform host/port discovery with Nmap, enumerate web directories with Gobuster/ffuf, and explain the OWASP Top 10 at a technical level.

---

## Phase 3 — Exploitation (Est. 25–30 hrs)

| Order | Note                                                   | Difficulty   | Est. Time | Prerequisites           |
| ----- | ------------------------------------------------------ | ------------ | --------- | ----------------------- |
| 18    | [[Metasploit]]                   | Intermediate | 3 hrs     | Phase 2 complete        |
| 19    | [[Burp-Suite]]                   | Intermediate | 3 hrs     | Web-Application-Testing |
| 20    | [[SQLmap]]                       | Intermediate | 2 hrs     | OWASP-Top-10            |
| 21    | [[Hydra]]                        | Intermediate | 2 hrs     | Common-Ports            |
| 22    | [[Netcat]]                       | Foundational | 1 hr      | TCP-IP-Fundamentals     |
| 23    | [[Reverse-Shell-Cheatsheet]] | Intermediate | 2 hrs     | Netcat                  |
| 24    | [[File-Transfer-Techniques]] | Intermediate | 1 hr      | Netcat                  |
| 25    | [[Social-Engineering]]    | Foundational | 1 hr      | None                    |
| 26    | [[Wireless-Security]]     | Intermediate | 2 hrs     | Networking-Basics       |

> [!important] Shell Stability
> After gaining a shell, **always upgrade to a stable PTY** before attempting post-exploitation. See [[Reverse-Shell-Cheatsheet]] for Python PTY and socat upgrade techniques.

---

## Phase 4 — Post-Exploitation and Active Directory (Est. 30–35 hrs)

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 27 | [[Linux-Privilege-Escalation]] | Advanced | 4 hrs | Phase 3 complete |
| 28 | [[Windows-Privilege-Escalation]] | Advanced | 4 hrs | Phase 3 complete |
| 29 | [[Active-Directory]] | Intermediate | 4 hrs | Windows-Privilege-Escalation |
| 30 | [[AD-Attack-Reference]] | Intermediate | 2 hrs | Active-Directory |
| 31 | [[BloodHound]] | Advanced | 2 hrs | Active-Directory |
| 32 | [[CrackMapExec]] | Advanced | 2 hrs | AD-Attack-Reference |
| 33 | [[Evil-WinRM]] | Intermediate | 1 hr | Active-Directory |
| 34 | [[Impacket]] | Advanced | 3 hrs | AD-Attack-Reference |
| 35 | [[Responder]] | Advanced | 2 hrs | AD-Attack-Reference |
| 36 | [[Hashcat]] | Intermediate | 2 hrs | Cryptography-Basics |
| 37 | [[John-the-Ripper]] | Foundational | 1 hr | Cryptography-Basics |

> [!tip] Phase 4 Checkpoint
> You should be able to: enumerate a Windows/AD environment from a low-privilege shell, identify privesc vectors using LinPEAS/WinPEAS, perform Pass-the-Hash attacks, and use BloodHound to map privilege escalation paths.

---

## Phase 5 — Advanced Techniques (Est. 15–20 hrs)

| Order | Note | Difficulty | Est. Time | Prerequisites |
|-------|------|-----------|-----------|---------------|
| 38 | [[Malware-Analysis]] | Advanced | 4 hrs | Phase 4 complete |
| 39 | [[Reverse-Engineering-Basics]] | Advanced | 4 hrs | Malware-Analysis |
| 40 | [[CTF-Binary-Exploitation]] | Advanced | 4 hrs | Reverse-Engineering-Basics |
| 41 | [[CTF-Crypto-Attacks]] | Advanced | 3 hrs | Cryptography-Basics |
| 42 | [[Cloud-Security-Fundamentals]] | Intermediate | 3 hrs | Phase 2 complete |

---

## Recommended Labs (Offensive Focus)

Work through these in order — earlier boxes use simpler techniques; later boxes require chaining.

| Order | Lab | Vault Note | Key Techniques |
|-------|-----|-----------|----------------|
| 1 | THM Vulnversity | [[THM-Vulnversity]] | File upload, basic privesc |
| 2 | THM Kenobi | [[THM-Kenobi]] | SMB, NFS, SUID privesc |
| 3 | HTB Lame | [[HTB-Lame]] | Metasploit, SMB exploit |
| 4 | HTB Jerry | [[HTB-Jerry]] | Tomcat, WAR upload |
| 5 | HTB Devel | [[HTB-Devel]] | FTP, IIS, Windows privesc |
| 6 | THM Blue | [[THM-Blue]] | EternalBlue, Metasploit |
| 7 | THM Relevant | [[THM-Relevant]] | SMB, token impersonation |
| 8 | HTB Bashed | [[HTB-Bashed]] | Web shell, sudo privesc |
| 9 | HTB Nibbles | [[HTB-Nibbles]] | CMS exploit, sudo privesc |
| 10 | HTB Optimum | [[HTB-Optimum]] | HFS, Windows kernel exploit |
| 11 | HTB Shocker | [[HTB-Shocker]] | Shellshock, sudo privesc |
| 12 | HTB Bastard | [[HTB-Bastard]] | Drupal RCE, advanced privesc |

---

## CTF Methodology

| Note | Purpose |
|------|---------|
| [[CTF-Methodology]] | General approach for any CTF challenge |
| [[CTF-Quick-Reference]] | Quick lookup during active challenges |
| [[CTF-Web-Exploitation]] | Web challenge techniques |
| [[CTF-Forensics-Techniques]] | Forensics challenges |
| [[CTF-Binary-Exploitation]] | Binary/pwn challenges |
| [[CTF-Crypto-Attacks]] | Crypto challenges |

---

## Recommended Certifications (In Order)

| Cert | Vault Note | Priority |
|------|-----------|---------|
| CompTIA Security+ | [[Security-Plus-SY0-701]] | First — baseline required |
| eJPT (INE) | [[eJPT-INE]] | Second — first offensive cert |
| OSCP (OffSec) | [[OSCP-PEN-200]] | Third — industry gold standard |
| CEH | [[CEH-v12]] | Optional — vendor-neutral |

---

## See Also
- [[Learning-Path-SOC-Analyst]]
- [[Learning-Path-ServiceNow-Security-Admin]]
- [[TechOps-MOC]]
- [[Dashboard]]
