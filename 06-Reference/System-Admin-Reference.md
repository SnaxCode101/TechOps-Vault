---
title: System Administrator Reference
type: reference
category: sysadmin
date: 2026-03-26
tags:
  - sysadmin
  - reference
  - servicenow
  - linux
  - windows
  - networking
  - itsm
  - cloud
  - virtualization
  - foundational
difficulty: foundational
---

# System Administrator Reference

> Comprehensive reference for System Administrator roles, with a focus on ServiceNow environments. Covers platform administration, OS fundamentals, networking, ITSM processes, and best practices.

---

## 1 — Core Principles

| Concept | Definition |
|---|---|
| Principle of Least Privilege | Users get the minimum access necessary to do their job — nothing more |
| Authentication | Verifying identity (password, MFA, SSO, biometric) |
| Authorization | Granting access to resources based on verified identity |
| Users | Individual accounts |
| Groups | Collections of users — assign roles to groups, not individuals where possible |
| Roles | Permissions assigned to users or groups |

---

## 2 — Networking Essentials

**Common ports (sysadmin view):**

| Port | Protocol | Use |
|---|---|---|
| 22 | SSH | Secure remote access to Linux/Unix |
| 25 | SMTP | Email sending |
| 53 | DNS | Domain name resolution |
| 80 | HTTP | Unencrypted web |
| 443 | HTTPS | Encrypted web |
| 3389 | RDP | Windows remote desktop |
| 389 | LDAP | Directory service |
| 636 | LDAPS | LDAP over SSL |
| 445 | SMB | Windows file sharing |

**DNS, DHCP, LDAP:**
- **DNS** — Resolves domain names to IP addresses. `/etc/hosts` for local overrides (Linux)
- **DHCP** — Assigns IPs dynamically. Static IPs for servers, DHCP for workstations
- **LDAP** — Directory service for user authentication and attribute lookup. ServiceNow integrates via LDAP for AD auth

---

## 3 — Authentication & Authorization Protocols

| Protocol | Type | Notes |
|---|---|---|
| LDAP | Directory protocol | User auth against Active Directory / OpenLDAP |
| SSO | Framework | Single sign-on — one credential for multiple apps |
| SAML | XML-based | Auth + authorization assertions between IdP and SP |
| OAuth | Token-based | Authorization (not authentication) — API access delegation |
| Kerberos | Ticket-based | Windows AD native auth — tickets expire, uses KDC |

> **SAML vs OAuth:** SAML = authentication (who are you). OAuth = authorization (what can you do). OpenID Connect extends OAuth to add authentication.

---

## 4 — ServiceNow Data Model

| Concept | Description |
|---|---|
| Table | Stores records of a specific type (e.g. `incident`, `sys_user`) |
| Record | Individual row in a table |
| Field | Attribute of a record (e.g. `short_description`, `priority`) |
| sys_id | Globally unique 32-char hex identifier for every record |
| Dictionary Entry | Defines field data type, length, and behavior |
| CMDB | Configuration Management DB — tracks CIs and relationships |

**Key table names:**

| Table | Purpose |
|---|---|
| `incident` | Incident records |
| `sn_si_incident` | Security Incident records |
| `sys_user` | User accounts |
| `sys_user_group` | Groups |
| `sys_user_has_role` | User-role mappings |
| `change_request` | Change management |
| `sc_request` | Service catalog requests |
| `cmdb_ci` | Configuration items |

---

## 5 — Update Sets

- Capture configuration changes for migration between instances (dev → test → prod)
- Do **not** capture data records (incidents, users, etc.) — only config/customisation
- Always **Preview** before committing — check for conflicts
- Always **Commit** in the correct order if multiple sets are interdependent
- Use **Remote Update Sets** to pull from another instance

```
Navigator → System Update Sets → Local Update Sets
```

---

## 6 — Access Control Lists (ACLs)

- Control read, write, create, delete access to tables and fields
- Evaluated order: **table-level ACL first**, then field-level
- ACLs can contain: role conditions, scripts, field conditions
- **Test ACLs:** Use impersonation — Impersonate User → test as that user
- `security_admin` role required to modify ACLs in scoped apps

```
Navigator → System Security → Access Control (ACL)
```

---

## 7 — Business Rules

Server-side scripts triggered by database operations (insert, update, delete, query).

| Type | When | Use case |
|---|---|---|
| Before | Before record saves | Modify field values before DB write |
| After | After record saves | Trigger notifications, related record updates |
| Async | Background, after save | Long-running tasks, avoid slowing the UI |
| Display | Before form loads | Set `g_scratchpad` values for client scripts |

```javascript
// Example: Set resolved time when incident closes
if (current.state == 6 && previous.state != 6) {
    current.resolved_at = new GlideDateTime();
}
```

---

## 8 — Client Scripts

Run in the browser (client-side). Used for form manipulation and validation.

| Type | Trigger |
|---|---|
| onLoad | When the form loads |
| onChange | When a specific field value changes |
| onSubmit | When the form is submitted |
| onCellEdit | When a list cell is edited |

```javascript
// Example: Make 'close_notes' mandatory when state = Closed
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading || newValue == '') return;
    if (newValue == '7') { // 7 = Closed
        g_form.setMandatory('close_notes', true);
    } else {
        g_form.setMandatory('close_notes', false);
    }
}
```

---

## 9 — UI Policies

Declarative alternative to client scripts for simple form logic.

- Make fields mandatory, read-only, or hidden based on conditions
- No scripting required for standard use cases
- Applied in order — first matching policy wins
- **UI Policy Actions** define what happens to each field when the policy fires

> Use UI Policies for simple show/hide/mandatory logic. Use Client Scripts for complex multi-field logic or API calls.

---

## 10 — Flow Designer

See dedicated note: [[ServiceNow-Flow-Designer]]

Key points:
- Replaces legacy Workflow Editor for all new automation
- Trigger → Flow Logic → Actions model
- Data Pills pass values between steps
- Subflows enable reusable logic across multiple flows

---

## 11 — GlideRecord

Server-side API for querying and manipulating ServiceNow tables.

```javascript
// Query pattern
var gr = new GlideRecord('incident');
gr.addQuery('priority', 1);           // Filter: priority = 1
gr.addQuery('state', '!=', 6);       // Filter: state not Closed
gr.orderByDesc('sys_created_on');    // Sort newest first
gr.setLimit(10);                     // Limit results
gr.query();
while (gr.next()) {
    gs.log(gr.number + ' - ' + gr.short_description);
    gr.state = 2;   // Set In Progress
    gr.update();
}

// Insert new record
var grNew = new GlideRecord('incident');
grNew.initialize();
grNew.short_description = 'Test incident from script';
grNew.priority = 2;
grNew.insert();
```

**Key GlideRecord methods:**

| Method | Purpose |
|---|---|
| `addQuery(field, value)` | Add WHERE condition |
| `addEncodedQuery(string)` | Add complex encoded query |
| `query()` | Execute the query |
| `next()` | Move to next record |
| `get(sys_id)` | Get single record by sys_id |
| `setValue(field, value)` | Set field value safely |
| `update()` | Save changes to existing record |
| `insert()` | Create new record |
| `deleteRecord()` | Delete current record |
| `getRowCount()` | Count matching records |

---

## 12 — Navigation Shortcuts

Type directly in the filter navigator (left sidebar search bar):

| Shortcut | Destination |
|---|---|
| `sys_properties.list` | System properties |
| `sys_user.list` | All users |
| `sys_user_group.list` | All groups |
| `sys_user_has_role.list` | User-role mappings |
| `sys_script.list` | Business rules |
| `sys_ui_policy.list` | UI policies |
| `sys_update_xml.list` | Update set records |
| `sys_log.list` | System log |
| `incident.list` | All incidents |
| `sn_si_incident.list` | All security incidents |

---

## 13 — Logging & Troubleshooting

**Log types:**

| Log | Access | Use |
|---|---|---|
| System Log — All | `sys_log.list` | General platform logs |
| Script Logs | Filter for `gs.log()` source | Debug output from scripts |
| Transaction Logs | `sys_transaction.list` | HTTP transactions, performance |
| Email Logs | `sys_email.list` | Outbound/inbound email |
| Flow Execution Log | Flow → Executions | Per-execution debug trail |

**Troubleshooting checklist:**

1. Check ACLs — user may lack access to table or field
2. Review client scripts and UI policies — form behaviour issues
3. Inspect business rules — unexpected field changes or errors
4. Validate update sets — config not appearing after migration
5. Confirm table and field names — typos cause silent failures
6. Use browser console (`F12`) for client-side JavaScript errors
7. Use impersonation to replicate the exact user's experience

---

## 14 — ITSM Process Summary

| Process | Goal | Key Table |
|---|---|---|
| Incident Management | Restore normal service ASAP | `incident` |
| Problem Management | Find and eliminate root cause | `problem` |
| Change Management | Control change lifecycle, minimise risk | `change_request` |
| Request Management | Handle service requests | `sc_request` |
| CMDB | Track CIs and their relationships | `cmdb_ci` |
| SLA Management | Track performance against agreed targets | `contract_sla` |

---

## 15 — Linux Essentials

```bash
# Navigation and files
ls -la          # List all files with permissions
cd /path        # Change directory
pwd             # Print working directory
mkdir -p dir    # Create directory (and parents)
rm -rf dir      # Remove directory recursively (caution)
cp -r src dst   # Copy recursively
mv src dst      # Move / rename

# Permissions
chmod 755 file          # rwxr-xr-x
chmod +x script.sh      # Make executable
chown user:group file   # Change owner

# Processes
ps aux          # All running processes
top / htop      # Live process monitor
kill -9 PID     # Force kill by PID
systemctl status sshd   # Service status
systemctl restart nginx # Restart service

# Logs
tail -f /var/log/syslog     # Live system log
journalctl -u sshd -n 50   # Last 50 lines for service
cat /var/log/auth.log       # Auth/login events

# Package management
apt update && apt upgrade   # Debian/Ubuntu
yum update                  # RHEL/CentOS
```

---

## 16 — Windows Server Essentials

```cmd
# Active Directory
net user username /domain           -- Query AD user
net group "Domain Admins" /domain   -- List group members
gpupdate /force                     -- Force Group Policy refresh
gpresult /r                         -- Show applied GPOs

# Services and processes
sc query servicename    -- Query service status
net start servicename   -- Start service
net stop servicename    -- Stop service
tasklist /svc           -- Processes with service names
taskkill /PID 1234 /F   -- Force kill process

# System info
systeminfo              -- Full system details
ipconfig /all           -- Network config
netstat -ano            -- Active connections with PIDs
wmic product get name   -- Installed software
```

---

## 17 — Virtualization

| Concept | Description |
|---|---|
| Type 1 Hypervisor | Bare metal — VMware ESXi, Microsoft Hyper-V, Xen |
| Type 2 Hypervisor | Hosted on OS — VMware Workstation, VirtualBox |
| Snapshot | Point-in-time VM state save — use before risky changes |
| vCPU / vRAM | Virtual CPU and RAM allocated to a VM |
| VM Template | Pre-configured VM image for rapid deployment |
| Live Migration | Move running VM between hosts (vMotion in VMware) |

---

## 18 — Cloud Fundamentals

| Provider | Key Services |
|---|---|
| AWS | EC2 (VMs), S3 (storage), VPC (networking), IAM (access) |
| Azure | VMs, Blob Storage, Azure AD, NSGs |
| GCP | Compute Engine, Cloud Storage, VPC, IAM |

**Shared responsibility model:**
- **IaaS:** Cloud = hardware/network. You = OS, apps, data
- **PaaS:** Cloud = hardware/OS/runtime. You = apps, data
- **SaaS:** Cloud = everything. You = data and access management

---

## 19 — Backup Strategies

| Type | Backs up | Archive bit | Restore |
|---|---|---|---|
| Full | Everything | Cleared | Full only |
| Incremental | Since last full/incremental | Cleared | Full + all incrementals |
| Differential | Since last full | Not cleared | Full + last differential |

**3-2-1 Rule:** 3 copies, 2 media types, 1 offsite.

**Disaster Recovery terms:**
- **RTO** (Recovery Time Objective) — how fast must you recover
- **RPO** (Recovery Point Objective) — how much data loss is acceptable
- **BCP** (Business Continuity Plan) — how the org functions during disruption

---

## 20 — Monitoring

| Metric | Tool / Command |
|---|---|
| CPU/RAM/Disk | `top`, `htop`, `vmstat`, `df -h`, Task Manager |
| Network | `netstat`, `ss`, `iftop`, Wireshark |
| Log aggregation | Splunk, ELK Stack, Graylog |
| Infrastructure monitoring | Nagios, Zabbix, Prometheus + Grafana |
| ServiceNow health | Instance Diagnostics, Transaction Logs |

---

## See Also

- ServiceNow-Flow-Designer
- Linux-Commands
- Windows-Commands
- Active-Directory
- Networking-Basics
- Incident-Response
- CIA-Triad
- Bash-Scripting
- Python-for-Security

---
*Note created: 2026-03-26*

---

## 21 — User & Group Management (Linux)

```bash
# Create user (with home dir and default shell)
sudo useradd -m -s /bin/bash username
sudo passwd username

# Create system user (no login, no home)
sudo useradd -r -s /usr/sbin/nologin serviceaccount

# Modify user
sudo usermod -aG sudo username       # Add to sudo group
sudo usermod -aG groupname username  # Add to group (keep existing)
sudo usermod -L username             # Lock account
sudo usermod -U username             # Unlock account
sudo usermod -e 2026-12-31 username  # Set expiry date

# Delete user
sudo userdel username                # Keep home dir
sudo userdel -r username             # Remove home dir too

# Create group
sudo groupadd groupname
sudo groupdel groupname

# Query
id username                          # UID, GID, groups
groups username                      # Group membership
cat /etc/passwd | grep username      # User record
getent passwd username               # Works with LDAP too
getent group groupname

# Key files
/etc/passwd       # User accounts (no passwords)
/etc/shadow       # Hashed passwords (root only)
/etc/group        # Group definitions
/etc/sudoers      # Sudo rules — edit with: sudo visudo
```

**`/etc/sudoers` patterns:**
```bash
# Full sudo without password (dangerous — avoid in prod)
username ALL=(ALL) NOPASSWD: ALL

# Allow specific command only
username ALL=(ALL) /usr/bin/systemctl restart nginx

# Allow group to sudo
%wheel ALL=(ALL) ALL
%sudo  ALL=(ALL) ALL
```

---

## 22 — Cron & Scheduled Tasks

### Linux — cron

```bash
# Edit current user's crontab
crontab -e

# Edit another user's crontab
sudo crontab -u username -e

# List crontabs
crontab -l
sudo crontab -u username -l

# Cron expression format:
# ┌─── minute (0-59)
# │ ┌─── hour (0-23)
# │ │ ┌─── day of month (1-31)
# │ │ │ ┌─── month (1-12)
# │ │ │ │ ┌─── day of week (0-6, Sun=0)
# │ │ │ │ │
# * * * * * /path/to/command

# Examples
0 2 * * *     /usr/bin/backup.sh          # Daily at 02:00
*/15 * * * *  /usr/bin/check_disk.sh      # Every 15 minutes
0 9 * * 1     /usr/bin/weekly_report.sh   # Monday 09:00
0 0 1 * *     /usr/bin/monthly.sh         # 1st of month at midnight
@reboot       /usr/bin/startup.sh         # At system boot

# System-wide cron locations
/etc/cron.d/          # Drop-in cron files
/etc/cron.daily/      # Scripts run daily (by run-parts)
/etc/cron.weekly/     # Scripts run weekly
/etc/cron.hourly/     # Scripts run hourly
/etc/crontab          # System crontab (includes username field)

# Cron logs
grep CRON /var/log/syslog
journalctl -u cron
```

### Linux — systemd timers (modern alternative)

```bash
# List all timers
systemctl list-timers --all

# Example timer unit: /etc/systemd/system/backup.timer
[Unit]
Description=Daily Backup Timer

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target

# Matching service: /etc/systemd/system/backup.service
[Unit]
Description=Daily Backup

[Service]
Type=oneshot
ExecStart=/usr/bin/backup.sh

# Enable and start
sudo systemctl enable --now backup.timer
sudo systemctl status backup.timer
```

### Windows — Task Scheduler

```powershell
# List scheduled tasks
Get-ScheduledTask
schtasks /query /fo LIST /v

# Create a task (PowerShell)
$action  = New-ScheduledTaskAction -Execute "C:\Scripts\backup.ps1"
$trigger = New-ScheduledTaskTrigger -Daily -At 2am
Register-ScheduledTask -TaskName "DailyBackup" -Action $action -Trigger $trigger -RunLevel Highest

# Via schtasks (cmd)
schtasks /create /tn "DailyBackup" /tr "C:\Scripts\backup.bat" /sc daily /st 02:00

# Delete task
schtasks /delete /tn "DailyBackup" /f

# Security note: enumerate scheduled tasks for persistence
schtasks /query /fo CSV | findstr /v "TaskName"
```

---

## 23 — Disk & Storage Management (Linux)

```bash
# Disk usage
df -h                    # Filesystem usage (human-readable)
df -h /var               # Specific mount point
du -sh /var/log          # Directory size
du -sh /* | sort -rh     # Top-level dirs, sorted by size

# Find large files
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null
find /var -type f -size +50M | xargs ls -lsh | sort -rh

# Block devices
lsblk                    # Tree view of block devices
fdisk -l                 # All disk/partition details
blkid                    # UUIDs and filesystem types

# Mount / unmount
mount /dev/sdb1 /mnt/data
umount /mnt/data
mount -a                 # Mount all entries in /etc/fstab

# /etc/fstab entry format:
# <device>    <mountpoint>  <fstype>  <options>      <dump> <pass>
UUID=abc123   /mnt/data     ext4      defaults,nodev  0      2

# Filesystem operations
mkfs.ext4 /dev/sdb1         # Format as ext4 (DESTRUCTIVE ⚠️)
fsck /dev/sdb1              # Check and repair filesystem (unmounted)
tune2fs -l /dev/sda1        # Filesystem info

# LVM (Logical Volume Manager)
pvs                         # Physical volumes
vgs                         # Volume groups
lvs                         # Logical volumes

# Extend a logical volume + filesystem
lvextend -L +10G /dev/vg0/lv_data
resize2fs /dev/vg0/lv_data  # Resize ext4
xfs_growfs /mnt/data        # Resize XFS (must be mounted)

# Swap
swapon --show               # Active swap
mkswap /dev/sdb2            # Create swap
swapon /dev/sdb2            # Enable swap
swapoff /dev/sdb2           # Disable swap
free -h                     # RAM + swap usage
```

---

## 24 — Log Management & Rotation

```bash
# journald (systemd logs)
journalctl                              # All logs
journalctl -u nginx                     # Service-specific
journalctl -p err                       # Errors only
journalctl --since "1 hour ago"         # Time-filtered
journalctl --since "2026-04-01 08:00"  # Since timestamp
journalctl -f                           # Follow (live)
journalctl --disk-usage                 # How much space logs use
journalctl --vacuum-size=500M          # Trim to 500MB
journalctl --vacuum-time=30d           # Delete logs older than 30 days

# Traditional syslog
tail -f /var/log/syslog
tail -f /var/log/auth.log
grep -i "error" /var/log/syslog | tail -50

# logrotate — controls rotation of log files
# Config: /etc/logrotate.conf + /etc/logrotate.d/
cat /etc/logrotate.d/nginx

# Example logrotate config:
/var/log/myapp/*.log {
    daily           # Rotate daily
    rotate 14       # Keep 14 files
    compress        # gzip rotated files
    delaycompress   # Compress previous log, not current
    missingok       # Don't error if log missing
    notifempty      # Skip if log is empty
    postrotate      # Run after rotation
        systemctl reload nginx
    endscript
}

# Force rotation manually (for testing)
sudo logrotate -f /etc/logrotate.d/nginx

# Remote log forwarding (rsyslog)
# /etc/rsyslog.conf — forward all to central syslog server
*.* @192.168.1.100:514    # UDP
*.* @@192.168.1.100:514   # TCP
```

---
<- [[Reference-MOC]]