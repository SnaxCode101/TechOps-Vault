---
title: CEH v12 — Certified Ethical Hacker
tags:
  - intermediate
  - cert
  - CEH
  - v12
  - EC-Council
  - ethical-hacker
  - footprinting
  - scanning
  - exploitation
topic: Certification Study
difficulty: intermediate
created: 2026-03-26
updated: 2026-03-26
source: EC-Council CEH v12 official exam blueprint
---

# 🕵️ CEH v12 — Certified Ethical Hacker

## Overview
The CEH (Certified Ethical Hacker) is EC-Council's flagship certification covering 20 hacking domains. It focuses on understanding attacker techniques in order to defend against them. More theory-heavy than OSCP but widely recognized in enterprise and government roles. CEH Practical exam adds hands-on validation.

**Exam format:**
- CEH Theory: 125 MCQ, 4 hours, passing ~70%
- CEH Practical (optional): 6-hour hands-on exam, 20 scenario challenges, 70% pass

**Cost:** ~$950–$1,200 USD (exam only, varies by region)

---

## 20 Exam Modules

| # | Module | Key Topics |
|---|---|---|
| 01 | Introduction to Ethical Hacking | Hacking phases, attacker types, laws, concepts |
| 02 | Footprinting and Reconnaissance | OSINT, Google dorks, WHOIS, Shodan, Maltego |
| 03 | Scanning Networks | Nmap, banner grabbing, vulnerability scanning |
| 04 | Enumeration | SMB, LDAP, SNMP, NFS, DNS zone transfer |
| 05 | Vulnerability Analysis | CVE, CVSS, Nessus, OpenVAS |
| 06 | System Hacking | Password cracking, privilege escalation, pivoting |
| 07 | Malware Threats | Viruses, worms, trojans, ransomware, APTs |
| 08 | Sniffing | Wireshark, ARP spoofing, MITM, passive sniffing |
| 09 | Social Engineering | Phishing, vishing, pretexting, impersonation |
| 10 | Denial-of-Service | DDoS, SYN flood, amplification attacks |
| 11 | Session Hijacking | TCP session hijack, cookie theft, CSRF |
| 12 | Evading IDS, Firewalls, Honeypots | Fragmentation, obfuscation, tunneling |
| 13 | Hacking Web Servers | Web server misconfigs, CVE exploitation |
| 14 | Hacking Web Applications | OWASP Top 10, SQLi, XSS, SSRF, XXE |
| 15 | SQL Injection | Union, blind, error-based, out-of-band SQLi |
| 16 | Hacking Wireless Networks | WEP/WPA2 cracking, evil twin, de-auth attacks |
| 17 | Hacking Mobile Platforms | Android/iOS attack surface, OWASP Mobile Top 10 |
| 18 | IoT and OT Hacking | IoT protocols, Shodan for IoT, OT/SCADA |
| 19 | Cloud Computing | AWS/Azure/GCP attacks, IAM, S3 misconfig |
| 20 | Cryptography | Encryption algorithms, PKI, TLS, crypto attacks |

---

## Module Deep Dives

### Module 02 — Footprinting
```bash
# Google dorks
site:target.com filetype:pdf
site:target.com inurl:admin
intitle:"Index of" site:target.com
"@target.com" "password"

# WHOIS
whois target.com

# DNS enumeration
nslookup -type=ANY target.com
dig target.com ANY
dig axfr @dns-server target.com     # Zone transfer attempt

# Shodan
shodan search "hostname:target.com"
shodan host IP_ADDRESS

# theHarvester
theHarvester -d target.com -b all
```

### Module 03 — Scanning
```bash
# TCP SYN scan
nmap -sS -p- target.com

# OS detection + version
nmap -A target.com

# Idle/zombie scan (spoofed source)
nmap -sI ZOMBIE_IP TARGET_IP

# Xmas scan (FIN, PSH, URG flags)
nmap -sX TARGET_IP

# Null scan (no flags)
nmap -sN TARGET_IP
```

### Module 04 — Enumeration
```bash
# SMB enumeration
enum4linux -a TARGET_IP
nmap --script smb-enum-users,smb-enum-shares TARGET_IP

# SNMP enumeration
snmpwalk -v2c -c public TARGET_IP
onesixtyone -c /usr/share/doc/onesixtyone/dict.txt TARGET_IP

# LDAP enumeration
ldapsearch -x -h TARGET_IP -b "DC=domain,DC=com"

# DNS zone transfer
dig axfr target.com @ns1.target.com

# NFS enumeration
showmount -e TARGET_IP
```

### Module 06 — System Hacking (4 Phases)
```
Phase 1: Gaining Access
  - Password attacks: brute-force, dictionary, rainbow tables
  - Exploitation: known CVEs, metasploit
  - Keyloggers, spyware

Phase 2: Escalating Privileges
  - DLL injection, token manipulation
  - SUID abuse (Linux), unquoted service paths (Windows)

Phase 3: Maintaining Access
  - Backdoors, rootkits, scheduled tasks, registry run keys
  - Netcat/Meterpreter persistent shells

Phase 4: Clearing Logs
  - Windows: wevtutil cl Security, wevtutil cl System
  - Linux: echo > /var/log/auth.log
  - Anti-forensics: Timestomping, file shredding
```

### Module 08 — Sniffing
```bash
# Passive sniffing — capture traffic on same segment
tcpdump -i eth0 -w capture.pcap
wireshark

# Active sniffing — ARP poisoning on switched network
arpspoof -i eth0 -t VICTIM_IP GATEWAY_IP
arpspoof -i eth0 -t GATEWAY_IP VICTIM_IP
# Then capture with tcpdump/wireshark

# Detect ARP spoofing (Blue Team)
arp -a   # Check for duplicate MAC addresses
```

### Module 15 — SQL Injection Types
| Type | Technique |
|---|---|
| Union-based | `' UNION SELECT null,null,table_name FROM information_schema.tables--` |
| Error-based | `' AND EXTRACTVALUE(1,CONCAT(0x7e,(SELECT version())))--` |
| Blind Boolean | `' AND 1=1--` vs `' AND 1=2--` |
| Blind Time-based | `'; IF(1=1) WAITFOR DELAY '0:0:5'--` (MSSQL) |
| Out-of-band | DNS/HTTP exfil using load_file() or UTL_HTTP |

### Module 16 — Wireless Attacks
```bash
# WPA2 handshake capture
airmon-ng start wlan0
airodump-ng wlan0mon -c [channel] --bssid [AP_MAC] -w capture
aireplay-ng -0 5 -a [AP_MAC] -c [CLIENT_MAC] wlan0mon   # De-auth
hashcat -m 22000 capture.hc22000 /usr/share/wordlists/rockyou.txt

# Evil twin / rogue AP
hostapd-wpe (enterprise attacks)
```

---

## EC-Council Hacking Methodology (5 Phases)
```
1. Reconnaissance    → Passive + Active info gathering
2. Scanning          → Port scan, vuln scan, OS/service detection
3. Gaining Access    → Exploitation
4. Maintaining Access → Persistence, backdoors
5. Clearing Tracks   → Log tampering, evidence removal
```

---

## Key CEH vs OSCP Differences

| Aspect | CEH | OSCP |
|---|---|---|
| Format | MCQ (theory-heavy) | Fully hands-on exam |
| Difficulty | Moderate | High |
| Industry recognition | Strong (gov/enterprise) | Strongest in pentest field |
| Practical skills | CEH Practical (add-on) | Core requirement |
| Best for | Compliance, HR checkboxes | Technical pentest roles |

---

## Study Resources

| Resource | Notes |
|---|---|
| EC-Council iLabs | Official labs included in training |
| Matt Walker CEH guide | Popular study book |
| TryHackMe CEH path | Hands-on reinforcement |
| CEHv12 Exam Cram (Pearson) | Quick reference |

---

## Related Notes
- Penetration-Testing-Methodology
- Nmap
- Wireshark
- Social-Engineering
- Web-Application-Testing
- Cryptography-Basics
- Cloud-Security-Fundamentals

## References
- EC-Council CEH v12 Exam Blueprint: https://www.eccouncil.org/programs/certified-ethical-hacker-ceh/

---
<- [[Certs-MOC]]