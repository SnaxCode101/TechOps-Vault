---
title: Home Lab Setup
tags: |-
  - project
  - home-lab
  - VirtualBox
  - VMware
  - Kali
  - Metasploitable
  - DVWA
  - lab-setup
topic: Projects
created: 2026-03-26
updated: 2026-03-26
---

# 🏠 Home Lab Setup

## Overview
A personal security home lab is essential for practicing techniques safely and legally. This note covers how to build a full attack/defense lab using free or low-cost tools on your existing hardware.

---

## Hypervisor Options

| Hypervisor | Cost | Best For |
|---|---|---|
| VirtualBox | Free | Beginners, cross-platform |
| VMware Workstation Pro | Free (since 2024) | Better performance, snapshots |
| VMware Workstation Player | Free | Basic usage |
| Hyper-V | Free (Windows Pro+) | Windows-native, AD labs |
| Proxmox | Free | Dedicated server, enterprise-grade |
| UTM | Free | macOS/Apple Silicon |

**Recommended: VirtualBox (free, cross-platform) or VMware Workstation Pro (free as of 2024)**

Download:
- VirtualBox: https://www.virtualbox.org/wiki/Downloads
- VMware: https://www.vmware.com/products/workstation-pro.html

---

## Recommended VMs

### Attack Machines
| VM | Purpose | Download |
|---|---|---|
| Kali Linux | Full offensive toolkit | https://www.kali.org/get-kali/ |
| Parrot OS (Security) | Lightweight alternative to Kali | https://parrotsec.org |
| BlackArch | Arch-based, huge tool library | https://blackarch.org |

### Vulnerable Target Machines
| VM | Purpose | Download |
|---|---|---|
| Metasploitable 2 | Intentionally vulnerable Linux | https://sourceforge.net/projects/metasploitable/ |
| Metasploitable 3 | More complex vulnerable machines | https://github.com/rapid7/metasploitable3 |
| DVWA (Docker) | Damn Vulnerable Web App | https://github.com/digininja/DVWA |
| VulnHub | Download vulnerable VMs | https://www.vulnhub.com |
| Windows 7/10 (eval) | Windows pentesting target | Microsoft Eval Center |

### Active Directory Lab
| VM | Purpose |
|---|---|
| Windows Server 2019/2022 (eval) | Domain Controller |
| Windows 10 (eval) × 2 | Domain-joined workstations |
| Kali Linux | Attacker machine |

```
Microsoft Evaluation Center:
https://www.microsoft.com/en-us/evalcenter/
→ Windows Server 2022 (180-day eval, free)
→ Windows 10 Enterprise (90-day eval, free)
```

---

## Network Configuration

### VirtualBox Network Types
| Mode | Description | Use Case |
|---|---|---|
| NAT | VM shares host's internet | Single VM, internet access needed |
| Host-Only | VM ↔ Host only, no internet | Isolated lab network |
| Internal Network | VM ↔ VM only | Fully isolated lab |
| NAT Network | Multiple VMs share NAT, can talk to each other | Lab + internet |
| Bridged | VM appears on physical network | Real network testing (caution) |

### Recommended Lab Network Setup
```
Network: 192.168.56.0/24 (Host-Only or Internal)
├── Kali Linux:         192.168.56.100 (static)
├── Metasploitable 2:   192.168.56.101 (static)
├── Win Server (DC):    192.168.56.10  (static)
├── Win 10 (WS1):       192.168.56.20  (DHCP or static)
└── Win 10 (WS2):       192.168.56.21  (DHCP or static)

Add a second adapter on Kali bridged/NAT for internet access
```

### VirtualBox Host-Only Network Setup
```
VirtualBox → File → Tools → Network Manager
→ Host-Only Networks → Create
→ Set: 192.168.56.0/24
→ Disable DHCP (set static IPs on VMs for lab stability)
```

---

## Kali Linux Initial Setup

```bash
# Update everything
sudo apt update && sudo apt full-upgrade -y

# Install commonly needed tools not in default
sudo apt install -y gobuster ffuf nikto impacket-scripts crackmapexec evil-winrm \
  seclists wordlists responder bloodhound neo4j zaproxy burpsuite

# Install Python packages
pip3 install impacket volatility3

# Set up Metasploit database
sudo msfdb init
sudo systemctl enable postgresql

# Install Obsidian (access your vault from Kali)
# Download AppImage from https://obsidian.md

# Set up SSH for remote access
sudo systemctl enable ssh
sudo systemctl start ssh
```

---

## DVWA Setup (Docker)

```bash
# Install Docker
sudo apt install docker.io docker-compose -y
sudo systemctl start docker

# Pull and run DVWA
docker pull vulnerables/web-dvwa
docker run -d -p 80:80 --name dvwa vulnerables/web-dvwa

# Access: http://localhost/login.php
# Default: admin / password
# Setup: http://localhost/setup.php → Create/Reset Database

# DVWA security levels:
# Low    → No protections (for learning)
# Medium → Some protections (for practicing bypasses)
# High   → Most protections (advanced)
# Impossible → Secure code (for comparison)
```

---

## Active Directory Lab Build

### Step 1: Install Windows Server 2022 (DC)
```
1. Create VM: 4GB RAM, 60GB disk, Windows Server 2022
2. Install Windows Server (Desktop Experience)
3. Set static IP: 192.168.56.10
4. Run Server Manager → Add Roles → Active Directory Domain Services
5. Promote to domain controller:
   - New forest: lab.local
   - DSRM password: (set secure password)
   - Reboot
```

### Step 2: Configure AD (Create Users, Groups, GPOs)
```powershell
# On DC — create test users
New-ADUser -Name "Alice Smith" -SamAccountName alice -UserPrincipalName alice@lab.local -Path "CN=Users,DC=lab,DC=local" -AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force) -Enabled $true
New-ADUser -Name "Bob Jones" -SamAccountName bob -UserPrincipalName bob@lab.local -Path "CN=Users,DC=lab,DC=local" -AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force) -Enabled $true

# Create groups
New-ADGroup -Name "IT Admins" -GroupCategory Security -GroupScope Global -Path "CN=Users,DC=lab,DC=local"

# Add user to Domain Admins (for attack practice)
Add-ADGroupMember -Identity "Domain Admins" -Members alice

# Set a weak SPN (for Kerberoasting practice)
Set-ADUser -Identity alice -ServicePrincipalNames @{Add="HTTP/webserver.lab.local"}
```

### Step 3: Join Windows 10 Workstations to Domain
```
1. Set DNS to DC IP (192.168.56.10)
2. System Properties → Change → Domain: lab.local
3. Reboot, log in with domain credentials
```

### Step 4: Configure AD Vulnerabilities (Practice)
```powershell
# Enable AS-REP roasting (disable pre-auth on a user)
Set-ADAccountControl -Identity bob -DoesNotRequirePreAuth $true

# Disable SMB signing (for relay attacks) — GPO
# Computer Config → Windows Settings → Security Settings → Local Policies → Security Options
# Microsoft network server: Digitally sign communications (always) = Disabled
```

---

## VPN / Remote Access (Optional)

```bash
# OpenVPN (connect to HTB, THM, etc.)
sudo openvpn lab.ovpn

# Verify connection
ip a show tun0
ping TARGET_IP

# Useful aliases (add to ~/.bashrc or ~/.zshrc)
alias vpn-htb='sudo openvpn ~/vpn/htb.ovpn'
alias vpn-thm='sudo openvpn ~/vpn/thm.ovpn'
```

---

## Useful Wordlists (Already on Kali)

```bash
/usr/share/wordlists/rockyou.txt.gz           # Password cracking (gunzip first)
/usr/share/wordlists/dirbuster/               # Directory brute force
/usr/share/seclists/                          # SecLists — comprehensive collection
  Discovery/Web-Content/                      # Web directories
  Passwords/                                  # Password lists
  Usernames/                                  # Username lists
  Fuzzing/                                    # Special chars, payloads

# Extract rockyou
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```

---

## Lab Safety Rules

> ⚠️ Always follow these to avoid accidental damage or legal issues.

1. **Never use vulnerable VMs on a real network** — host-only or internal only
2. **Take snapshots before every exploit** — restore quickly after testing
3. **Do not expose lab VMs to internet** — no bridged adapters on vulnerable machines
4. **Keep attack tools on Kali only** — not on host machine
5. **Only practice on systems you own** — lab VMs, HTB, THM, VulnHub
6. **Document everything** — use Learning Log for sessions

---

## Related Notes
- Penetration-Testing-Methodology
- Active-Directory
- Kali Linux (implicit via Linux-Commands)
- Learning-Log-Index
- Project-Template

---
<- [[Projects-MOC]]