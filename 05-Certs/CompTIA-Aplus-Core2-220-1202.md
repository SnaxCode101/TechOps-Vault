---
title: CompTIA A+ Core 2 (220-1202) Study Guide
tags:
  - certs
  - CompTIA
  - Aplus
  - operating-systems
  - security
  - study-guide
  - foundational
topic: CompTIA A+ Core 2
difficulty: foundational
created: 2026-04-03
updated: 2026-04-03
source: CompTIA A+ Core 2 (220-1202) V15 Official Exam Objectives
---

# CompTIA A+ Core 2 (220-1202) Study Guide

## Overview
The CompTIA A+ Core 2 exam (220-1202) covers operating systems, security, software troubleshooting, and operational procedures. Passing score: **700** on a scale of 100–900. Max 90 questions, 90 minutes.

**Exam weight by domain:**
| Domain | Weight |
|--------|--------|
| Operating Systems | 28% |
| Security | 28% |
| Software Troubleshooting | 23% |
| Operational Procedures | 21% |

---

## Domain 1 — Operating Systems (28%)

### 1.1 OS Types and File Systems

**Workstation OS types:**
| OS | Key Facts |
|----|-----------|
| Windows | Dominant enterprise desktop; Active Directory integration |
| Linux | Open-source; many distros; command-line strength; servers and desktops |
| macOS | Apple hardware only; Unix-based; strong security model |
| Chrome OS | Cloud-based; Linux kernel; runs Android apps; Chromebook |

**Mobile OS types:**
| OS | Key Facts |
|----|-----------|
| iOS | Apple; closed ecosystem; App Store only (default) |
| iPadOS | iPad-specific; split-screen, Apple Pencil support |
| Android | Open-source; Google; sideloading allowed; OEM customization |

**File systems:**
| File System | Max File Size | Notes |
|-------------|--------------|-------|
| NTFS | 16 TB | Windows standard; permissions, encryption, journaling |
| ReFS | 16 EB | Windows Server/workstation; resilient; auto-repair |
| FAT32 | 4 GB | USB drives; broad compatibility |
| exFAT | 16 EB | Flash drives; no NTFS overhead; cross-platform |
| ext4 | 16 TB | Linux standard |
| XFS | 8 EB | Linux; high performance; large files |
| APFS | — | macOS/iOS; SSD-optimized; strong encryption |

**Vendor life-cycle:**
- **EOL (End-of-Life):** Vendor stops security updates; system becomes vulnerable; must upgrade
- **Update limitations:** Some older hardware cannot run newer OS versions
- **Compatibility:** Cross-OS issues (file formats, permissions, app availability)

### 1.2 OS Installation and Upgrades

**Boot methods:** USB (preferred), network (PXE), solid-state/flash, internet-based, external hot-swappable drive, internal partition, multiboot.

**Installation types:**
| Type | Notes |
|------|-------|
| Clean install | Wipes existing OS; fresh start |
| Upgrade | Preserves files, settings, apps; in-place |
| Image deployment | WIM/ESD image via WDS, SCCM, MDT |
| Remote network install | PXE boot; no physical media |
| Zero-touch deployment | Fully automated; no technician involvement; Autopilot |
| Recovery partition | OEM restore to factory state |
| Repair installation | Reinstalls Windows preserving data; fixes system files |
| Third-party drivers | May be needed during install for RAID controllers, NICs |

**Partition styles:**
| Style | Notes |
|-------|-------|
| MBR | Legacy; max 4 primary partitions; max 2 TB |
| GPT | Modern; required for UEFI boot and disks >2 TB; unlimited partitions |

**Upgrade considerations:** Back up files/preferences, verify app/driver backward compatibility, check hardware compatibility (TPM, CPU generation, RAM).

**Feature updates:** Windows releases semi-annual feature updates; product life cycle determines support end date.

### 1.3 Windows Editions

**Windows 10/11 editions:**
| Edition | Domain Join | BitLocker | Hyper-V | gpedit.msc | RDP | RAM Limit |
|---------|------------|-----------|---------|------------|-----|-----------|
| Home | No | No | No | No | Client only | 128 GB |
| Pro | Yes | Yes | Yes | Yes | Yes | 2 TB |
| Pro for Workstations | Yes | Yes | Yes | Yes | Yes | 6 TB |
| Enterprise | Yes | Yes | Yes | Yes | Yes | 6 TB |

**Windows 11 hardware requirements:** TPM 2.0, Secure Boot, 64-bit CPU (8th gen Intel / Ryzen 2000+), 4 GB RAM, 64 GB storage, DirectX 12 GPU.

**N versions:** European editions without Windows Media Player (media licensing laws).

**Domain vs workgroup:** Domain = centralized AD authentication and policy; Workgroup = peer-to-peer; no central management.

**Upgrade paths:** In-place upgrade (preserves data) or clean install. Home → Pro requires license purchase.

### 1.4 Windows OS Tools (MMC snap-ins)
| Tool | Command | Purpose |
|------|---------|---------|
| Task Manager | Ctrl+Shift+Esc | Processes, performance, startup, users, services |
| Event Viewer | eventvwr.msc | System, security, application logs |
| Disk Management | diskmgmt.msc | Partition, format, assign drive letters |
| Task Scheduler | taskschd.msc | Schedule automated tasks |
| Device Manager | devmgmt.msc | Hardware drivers; enable/disable; update |
| Certificate Manager | certmgr.msc | Manage user certificates |
| Local Users and Groups | lusrmgr.msc | User and group management (Pro+) |
| Performance Monitor | perfmon.msc | Long-term performance data collection |
| Group Policy Editor | gpedit.msc | Local policy (Pro/Enterprise only) |
| System Information | msinfo32.exe | Hardware and software summary |
| Resource Monitor | resmon.exe | Detailed CPU/RAM/disk/network |
| System Configuration | msconfig.exe | Boot options; startup programs |
| Disk Cleanup | cleanmgr.exe | Remove temp files, old updates |
| Disk Defragment | dfrgui.exe | Defrag HDD (not SSD) |
| Registry Editor | regedit.exe | Edit Windows registry |

### 1.5 Windows Command-Line Tools
```cmd
:: Navigation
cd <path>     dir     md     rmdir     robocopy

:: Network
ipconfig      ping      netstat      nslookup
net use       tracert   pathping

:: Disk management
chkdsk C: /f /r       :: Check and fix disk errors
format                :: Format volume
diskpart              :: Partition CLI

:: File management
md    rmdir    robocopy

:: Informational
hostname    net user    winver    whoami
[command] /?          :: Get help for any command

:: OS management
gpupdate /force       :: Apply Group Policy immediately
gpresult /r           :: Show applied Group Policy
sfc /scannow          :: Repair system files
DISM /Online /Cleanup-Image /RestoreHealth  :: Repair Windows image
```

**pathping:** Combines ping and tracert; shows latency AND packet loss at each hop.
**net use:** Map/disconnect network drives; `net use Z: \\server\share`

### 1.6 Windows Settings
Key Control Panel / Settings areas to know: Internet Options, Devices and Printers, Programs and Features, Network and Sharing Center, Windows Defender Firewall, User Accounts, Device Manager, Administrative Tools, File Explorer Options (view hidden files, hide extensions), Power Options (hibernate, sleep, power plans, fast startup, USB selective suspend), Update and Security, System, Accounts, Privacy.

**Power states:**
| State | Description |
|-------|-------------|
| Sleep/Suspend | RAM powered; fast resume; low power |
| Hibernate | RAM saved to disk; no power draw; slower resume |
| Standby | Similar to sleep; older term |
| Fast startup | Hybrid shutdown; saves kernel state; faster boot |

### 1.7 Windows Networking
- **Domain joined:** Uses AD credentials; access to shared resources (files, printers, mapped drives)
- **Workgroup:** Local accounts; manual share setup
- **Mapped drives:** Persistent network path mapped to drive letter; `net use Z: \\server\share /persistent:yes`
- **Proxy settings:** Directs web traffic through proxy server; configurable per-browser or system-wide
- **Public vs private network:** Public = more restrictive firewall; Private = allows sharing
- **Metered connection:** Limits background data sync; conserves bandwidth (cellular/limited plans)
- **File Explorer network paths:** `\\server\share` or `\\192.168.1.10\share`
- **VPN:** Establishes encrypted tunnel; split or full tunnel

### 1.8 macOS Features
| Feature | Description |
|---------|-------------|
| Finder | File manager |
| Spotlight | System-wide search (Cmd+Space) |
| Mission Control | All open windows/spaces overview |
| Multiple Desktops | Virtual workspaces (Spaces) |
| Time Machine | Incremental backup to external drive |
| FileVault | Full disk encryption |
| Keychain | Built-in password/credential manager |
| Gatekeeper | Controls which apps can run |
| iCloud | Sync across Apple devices; iMessage, FaceTime, Drive |
| Gestures | Multi-touch trackpad gestures |
| Dock | App launcher/switcher at screen edge |
| Continuity | Handoff between Mac and iPhone/iPad |
| Disk Utility | Format, repair, partition disks |
| Terminal | Command-line (bash/zsh) |
| Force Quit | Cmd+Option+Esc; kill unresponsive apps |
| Rapid Security Response (RSR) | Quick security patches without full OS update |

**macOS file types:** .dmg (disk image), .pkg (installer), .app (application bundle).

**macOS system folders:** /Applications, /Users, /Library, /System, /Users/Library.

**Apple ID:** Required for App Store, iCloud, FaceTime; corporate restrictions via MDM profiles.

**macOS best practices:** Enable Time Machine, keep AV installed, apply updates/RSR promptly.

### 1.9 Linux Essentials
```bash
# File management
ls -la    pwd    mv    cp    rm    chmod    chown    grep    find

# Filesystem management
fsck /dev/sda1    :: Check/repair filesystem (run on unmounted disk)
mount /dev/sda1 /mnt  :: Mount filesystem
umount /mnt           :: Unmount

# Administrative
su         :: Switch to root (substitutes user)
sudo       :: Run command as root (superuser do)

# Package management
apt install <pkg>    apt update    apt upgrade    :: Debian/Ubuntu
dnf install <pkg>    dnf update                   :: RHEL/Fedora/CentOS

# Networking
ip addr    ping    curl    dig    traceroute

# Informational
man <cmd>     :: Manual pages
cat           :: Display file contents
top           :: Interactive process viewer
ps aux        :: All running processes
du -sh /path  :: Directory size
df -h         :: Disk free space

# Text editor
nano <file>   :: Simple terminal text editor
```

**Common Linux config files:**
| File | Purpose |
|------|---------|
| /etc/passwd | User account info (no passwords) |
| /etc/shadow | Hashed passwords; root-only readable |
| /etc/hosts | Local hostname-to-IP mapping |
| /etc/fstab | Filesystem mount table; auto-mounts at boot |
| /etc/resolv.conf | DNS server configuration |

**Linux OS components:**
- **Kernel:** Core OS; manages hardware, processes, memory
- **Bootloader:** GRUB; loads kernel at startup
- **systemd:** Init system; manages services; `systemctl start/stop/enable/disable/status <service>`
- **Root account:** UID 0; unrestricted access; use sudo instead of logging in as root

### 1.10 Application Installation
**System requirements:** 32-bit vs 64-bit (64-bit apps won't run on 32-bit OS), dedicated vs integrated GPU, VRAM requirements, RAM, CPU speed/cores, storage, OS compatibility.

**Distribution methods:** Physical media / ISO file, downloadable package, image deployment.

**Impact considerations for new applications:**
- **Device:** Performance hit; storage consumption; driver conflicts
- **Network:** Bandwidth usage; firewall rule requirements
- **Operation:** Service dependencies; boot time impact
- **Business:** Licensing costs; compliance requirements; support implications

### 1.11 Cloud-Based Productivity Tools
- **Email systems:** Microsoft 365 (Exchange Online), Google Workspace (Gmail); configured via server address/OAuth
- **Storage:** OneDrive, Google Drive, Dropbox; configure sync folder settings and selective sync
- **Collaboration tools:** Spreadsheets (Excel Online, Sheets), Videoconferencing (Teams, Zoom, Meet), Presentation (PowerPoint Online, Slides), Word processing (Word Online, Docs), Instant messaging (Teams, Slack)
- **Identity synchronization:** Azure AD Connect syncs on-prem AD to cloud; Single Sign-On across apps
- **Licensing assignment:** Per-user or per-device licenses; assign via admin portal

---

## Domain 2 — Security (28%)

### 2.1 Security Measures

**Physical security:**
| Control | Purpose |
|---------|---------|
| Bollards | Prevent vehicle ramming attacks |
| Access control vestibule | Double-door entry; prevents tailgating; mantrap |
| Badge reader | RFID/smart card access control |
| Video surveillance | Cameras; deterrence and forensics |
| Alarm systems | Intrusion detection |
| Motion sensors | Trigger alarms/cameras |
| Door locks | Cipher lock, electromagnetic, smart lock |
| Equipment locks | Kensington cable lock; rack locks |
| Security guards | Physical presence |
| Fences | Perimeter control |
| Lighting | Deters and enables surveillance |
| Magnetometers | Metal detectors at entry points |

**Physical access credentials:**
- Key fobs, smart cards, mobile digital key, standard keys
- **Biometrics:** Retina scanner, fingerprint, palm print, facial recognition (FRT), voice recognition

**Logical security concepts:**
| Concept | Definition |
|---------|-----------|
| Principle of least privilege | Grant only minimum permissions needed |
| Zero Trust | Never trust, always verify; assumes breach; verify every access request |
| ACLs | Access Control Lists; define who can access what resources |
| MFA | Multifactor Authentication; 2+ factors required |
| SAML | Security Assertions Markup Language; federated identity standard for SSO |
| SSO | Single Sign-On; one credential set accesses multiple systems |
| Just-in-time access | Temporary elevated access granted only when needed |
| PAM | Privileged Access Management; controls and audits admin/privileged accounts |
| MDM | Mobile Device Management; corporate control of mobile devices |
| DLP | Data Loss Prevention; monitors and blocks sensitive data exfiltration |
| IAM | Identity Access Management; comprehensive framework for managing digital identities |
| Directory services | LDAP-based (Active Directory); centralizes authentication |

**MFA factors:**
- Something you know: password, PIN
- Something you have: hardware token, authenticator app (TOTP), OTP via SMS/email/voice call
- Something you are: biometrics
- **TOTP:** Time-based One-Time Password; changes every 30 seconds (Google Authenticator, Authy)
- **OTP:** One-time passcode; single use

### 2.2 Windows OS Security Settings

**Defender Antivirus:** Activate/deactivate via Windows Security; update definitions manually or via Windows Update.

**Windows Firewall:** Activate/deactivate per profile; create inbound/outbound rules by port, program, or protocol; managed via wf.msc.

**User types:**
| Account | Privileges |
|---------|-----------|
| Administrator | Full system control |
| Standard | Limited; cannot install most software or change system settings |
| Guest | Very limited; typically disabled by default |
| Power User | Legacy; between standard and admin |
| Local vs Microsoft account | Local = offline; Microsoft account = cloud sync, OneDrive, Store |

**Login options:** Username/password, PIN, fingerprint, facial recognition, SSO, Windows Hello (passwordless via biometric or PIN).

**NTFS permissions:**
| Permission | Files | Folders |
|------------|-------|---------|
| Read | View content | List contents |
| Write | Modify | Create files |
| Read & Execute | Run executables | Traverse |
| Modify | Read+Write+Delete | Read+Write+Delete |
| Full Control | All | All |

- **Inheritance:** Child objects inherit parent permissions by default
- **File/folder attributes:** Read-only, hidden, system, archive (used by backup software)
- **Local access:** Only NTFS permissions apply
- **Network access:** Most restrictive of NTFS AND Share permissions wins

**Share permissions:** Read, Change, Full Control. Simpler than NTFS; applies to network access only.

**Run as Administrator:** Right-click option; UAC prompt; allows elevated execution without logging in as admin.

**UAC (User Account Control):** Prevents unauthorized elevation; prompts for admin credentials; never disable on shared machines.

**BitLocker:** Full volume encryption; requires TPM 2.0; recovery key required if TPM fails.
**BitLocker To Go:** Encrypts removable drives (USB); does not require TPM.
**EFS (Encrypting File System):** NTFS file/folder encryption; tied to user certificate; green filename in Explorer.

**Active Directory:**
- **Joining domain:** System → About → Domain/Workgroup; requires domain admin credentials
- **Log-in script:** Runs at user logon; maps drives, sets environment
- **Organizational Units (OUs):** Containers for organizing AD objects
- **Home folders:** Personal network share mapped at logon
- **Group Policy:** Centrally managed settings pushed to domain computers/users
- **Security groups:** Control access to resources by group membership
- **Folder redirection:** Redirects user folders (Desktop, Documents) to network share

### 2.3 Wireless Security Protocols
| Protocol | Encryption | Authentication | Status |
|----------|-----------|----------------|--------|
| WEP | RC4 | PSK | Broken — never use |
| WPA | TKIP (RC4) | PSK | Deprecated |
| WPA2-Personal | AES-CCMP | PSK | Current minimum |
| WPA2-Enterprise | AES-CCMP | 802.1X + RADIUS | Corporate standard |
| WPA3-Personal | SAE | Dragonfly | Current best |
| WPA3-Enterprise | AES-256-GCMP | 802.1X + RADIUS | Highest security |

**Authentication servers:**
- **RADIUS:** Remote Authentication Dial-in User Service; centralized 802.1X auth; UDP ports 1812/1813
- **TACACS+:** Terminal Access Controller Access-Control System Plus; Cisco-preferred; TCP; encrypts entire payload (RADIUS only encrypts password)
- **Kerberos:** Windows AD authentication protocol; ticket-based; uses port 88; single sign-on within domain

### 2.4 Malware Types
| Type | Behavior | Key Indicator |
|------|----------|--------------|
| Trojan | Disguised as legitimate software | Unexpected processes; outbound connections |
| Rootkit | Hides at OS/kernel level | Hidden processes; AV disabled |
| Virus | Attaches to files; spreads on execution | Modified files |
| Spyware | Monitors user activity | Credential theft; data exfiltration |
| Ransomware | Encrypts files; demands payment | Files renamed; ransom note |
| Keylogger | Records keystrokes | Credential theft |
| Boot sector virus | Infects MBR/boot sector | Prevents OS from loading; runs before OS |
| Cryptominer | Uses CPU/GPU for cryptocurrency | 100% CPU; high power usage |
| Stalkerware | Tracks user location/activity covertly | Common in domestic abuse scenarios |
| Fileless | Operates in memory; no disk artifact | PowerShell abuse; LOLBins |
| Adware | Displays unwanted ads | Browser pop-ups; redirects |
| PUP | Potentially Unwanted Program | Toolbars; search hijacks |

**Detection and response tools:**
| Tool | Description |
|------|-------------|
| Antivirus | Signature-based detection |
| Anti-malware | Broader behavioral detection |
| EDR | Endpoint Detection and Response; continuous monitoring; behavioral analysis |
| MDR | Managed Detection and Response; EDR + human analysts |
| XDR | Extended Detection and Response; cross-platform (endpoint + network + cloud) |
| Email security gateway | Filters malicious email before delivery |
| Software firewall | Host-based traffic filtering |
| Recovery console | Boot to repair environment; remove malware without loading infected OS |

### 2.5 Social Engineering, Threats, Vulnerabilities

**Social engineering attacks:**
| Attack | Description |
|--------|-------------|
| Phishing | Bulk deceptive email |
| Spear phishing | Targeted phishing at specific individual |
| Whaling | Phishing targeting executives/C-suite |
| Vishing | Voice phishing (phone call) |
| Smishing | SMS phishing |
| QR code phishing | Malicious QR codes redirect to phishing site |
| Shoulder surfing | Watching screen/keyboard |
| Tailgating | Following authorized person through secure door |
| Impersonation | Pretending to be IT/vendor/authority |
| Dumpster diving | Recovering sensitive info from trash |
| Pretexting | Fabricated scenario to extract info |

**Technical threats:**
| Threat | Description |
|--------|-------------|
| DoS | Single-source flood; exhausts resources |
| DDoS | Distributed denial of service; botnet-based |
| Evil twin | Rogue AP mimicking legitimate SSID; MITM |
| Zero-day | Exploit for unknown/unpatched vulnerability |
| Spoofing | Forging IP/MAC/email address |
| On-path attack | MITM; intercepts and potentially modifies traffic |
| Brute-force | Try all password combinations |
| Dictionary attack | Try common words/passwords |
| Insider threat | Trusted user misusing access |
| SQL injection | Malicious SQL in input fields |
| XSS (Cross-site scripting) | Inject scripts into web pages viewed by others |
| Business Email Compromise (BEC) | Impersonate exec/vendor via email; fraudulent wire transfers |
| Supply chain/pipeline attack | Compromise software update or build process (e.g., SolarWinds) |

**Vulnerabilities:**
- Non-compliant systems, unpatched systems, missing AV/firewall
- EOL systems (no security updates)
- BYOD (personal devices with corporate data; harder to control)

### 2.6 SOHO Malware Removal Process (10 steps — exam order)
```
1.  Investigate and verify malware symptoms
2.  Quarantine the infected system (disconnect from network)
3.  Disable System Restore in Windows Home
4.  Remediate infected systems
5.  Update anti-malware software
6.  Scan and use removal techniques (Safe Mode, preinstallation environment)
7.  Reimage/reinstall if remediation fails
8.  Schedule scans and run updates
9.  Enable System Restore and create restore point
10. Educate the end user
```

### 2.7 Workstation Security and Hardening
- **Data-at-rest encryption:** BitLocker, EFS, FileVault
- **Password policy:** Length (12+ chars), complexity, uniqueness, expiration, history
- **BIOS/UEFI password:** Prevents unauthorized boot or BIOS changes
- **Screensaver lock:** Activate after inactivity; require password to unlock
- **Log off when not in use:** Prevents session hijacking
- **Secure/protect hardware:** Cable locks; physical access controls
- **Protect PII:** Store securely; encrypt; clean desk policy; shred documents
- **Password managers:** Encourage use; reduces password reuse
- **Account management:** Restrict permissions, restrict login times, disable guest, failed attempts lockout, timeout/screen lock, account expiration dates
- **Change default admin password:** First step on any new device
- **Disable AutoRun:** Prevents automatic execution of removable media
- **Disable unused services:** Reduces attack surface

### 2.8 Mobile Device Security
- **Device encryption:** Encrypts all data at rest; standard on modern iOS/Android
- **Screen locks:** Facial recognition, PIN, fingerprint, pattern, swipe (weakest)
- **Configuration profiles:** MDM-pushed settings; enforce encryption, VPN, restrictions
- **Patch management:** Keep OS and apps updated
- **Endpoint security:** AV, anti-malware, content filtering on device
- **Locator applications:** Find My (iOS), Find My Device (Android); GPS tracking
- **Remote wipe:** Erase device remotely if lost/stolen; MDM or built-in
- **Remote backup:** Ensure data backed up before wipe
- **Failed login restrictions:** Auto-wipe after N failed attempts
- **MDM policies:** BYOD vs corporate-owned; profile security requirements; enforce encryption/passcode

### 2.9 Data Destruction and Disposal
| Method | Media | Notes |
|--------|-------|-------|
| Drilling | HDD, SSD | Physical; punches through platters/chips |
| Shredding | HDD, SSD, paper | Most thorough physical destruction |
| Degaussing | HDD, magnetic tape | Strong magnetic field; destroys data + makes drive unusable |
| Incineration | Any | Ultimate destruction |
| Erasing/wiping | HDD (multiple passes) | Software overwrite; DoD 5220.22-M (7-pass) |
| Low-level format | HDD | Write zeros to all sectors |
| Standard format | Any | Only removes file system references; data recoverable |

> **SSDs cannot be reliably wiped by overwrite** — use manufacturer secure erase (ATA Secure Erase) or physical destruction.

**Outsourcing:** Use certified third-party vendor; require Certificate of Destruction/Recycling.
**Regulatory requirements:** HIPAA, PCI-DSS, GDPR dictate data retention and destruction requirements.

### 2.10 SOHO Network Security
- **Change default passwords:** First step on any router/AP
- **IP filtering:** Allow/deny specific IP ranges
- **Firmware updates:** Keep router firmware current for security patches
- **Content filtering:** Block categories of websites
- **Physical placement:** Secure in locked location; prevent physical tampering
- **Disable UPnP:** Prevents automatic port forwarding from malicious apps
- **Screened subnet (DMZ):** Isolated network segment for publicly accessible servers
- **Secure management access:** HTTPS admin panel; disable HTTP management; restrict admin access by IP
- **SSID change:** Use non-identifying SSID (not your name/address)
- **Disable SSID broadcast:** Minor obscurity; not real security
- **Encryption settings:** WPA3 minimum; WPA2-AES acceptable
- **Guest network:** Isolates guest/IoT from main LAN
- **Firewall:** Disable unused ports; configure port forwarding/mapping only as needed

### 2.11 Browser Security
- **Download from trusted sources only:** Verify hash of downloaded installer (SHA-256)
- **Avoid untrusted sources:** Third-party app stores, unofficial download sites
- **Browser patching:** Keep browser updated; enable auto-update
- **Extensions/plug-ins:** Only from trusted sources (Chrome Web Store, Firefox Add-ons); audit regularly; remove unused
- **Password managers:** Built-in (browser) or third-party (1Password, Bitwarden); better than reusing passwords
- **Secure connections:** HTTPS only; valid certificate required; check for padlock
- **Certificate warnings:** Do not proceed if invalid cert; MITM or misconfigured site
- **Pop-up blocker:** Enable; allow only trusted sites
- **Clear browsing data/cache:** Removes stored passwords, cookies, history; privacy hygiene
- **Private browsing mode:** Incognito; does not save local history/cookies; ISP/employer can still see traffic
- **Sign-in/sync:** Browser data sync across devices; use strong account password + MFA
- **Ad blockers:** Reduce malvertising risk; improve performance
- **Proxy settings:** Route browser traffic through proxy (corporate inspection or privacy)
- **Secure DNS:** DNS over HTTPS (DoH); prevents DNS snooping; configure via browser settings
- **Disable unused features/extensions:** Reduces attack surface

---

## Domain 3 — Software Troubleshooting (23%)

### 3.1 Windows OS Issues
| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| BSOD | Driver crash, RAM, kernel corruption | Event Viewer; update/rollback driver; memtest86 |
| Degraded performance | Low resources, malware, fragmentation | Task Manager; Defender scan; Disk Defrag (HDD) |
| Boot issues | Corrupted BCD, failed drive | WinRE Startup Repair; bootrec; check drive |
| Frequent shutdowns | Thermal, PSU, driver | Check temps; Event Viewer |
| Services not starting | Dependency failure, corrupted service | services.msc; check dependencies; sfc /scannow |
| Applications crashing | Corrupted install, .NET issue | Reinstall; update .NET/VC++ Redistributables; Event Viewer |
| Low memory warnings | Insufficient RAM, memory leak | Close apps; add RAM; check for memory leaks |
| USB controller resource warnings | IRQ conflict, driver issue | Device Manager; update chipset drivers |
| System instability | Multiple possible causes | sfc /scannow; DISM; check drivers |
| No OS found | Boot order, failed drive, corrupted BOM | Check BIOS boot order; WinRE; bootrec /rebuildbcd |
| Slow profile load | Roaming profile issue, corrupt profile | Create new local profile; copy data; check network path |
| Time drift | CMOS battery failure, NTP issue | Replace CMOS battery; sync with NTP server |

**Windows boot repair commands:**
```cmd
bootrec /scanos         :: Scan for Windows installs
bootrec /rebuildbcd     :: Rebuild Boot Configuration Data
bootrec /fixmbr         :: Repair MBR
bootrec /fixboot        :: Repair boot sector
sfc /scannow            :: Repair system files
DISM /Online /Cleanup-Image /RestoreHealth  :: Repair Windows image
```

**Safe Mode access:** Shift+Restart → Troubleshoot → Advanced Options → Startup Settings → F4 (Safe Mode) / F5 (with Networking) / F6 (with Command Prompt).

**WinRE options:** Startup Repair, System Restore, System Image Recovery, Command Prompt, Reset this PC.

### 3.2 Mobile OS and Application Issues
| Symptom | Cause | Fix |
|---------|-------|-----|
| App fails to launch | Corrupted install, low memory | Clear cache; reinstall |
| App fails to close/crashes | Memory leak, bug | Force close; update; reinstall |
| App fails to update | Insufficient storage, network | Free storage; check connection |
| App fails to install | Incompatible OS version, storage | Check requirements; free space |
| Slow to respond | Low storage, background apps | Free storage (keep >10%); close apps |
| OS fails to update | Insufficient storage, network | Free space; connect to Wi-Fi |
| Battery life issues | Background apps, aging battery, settings | Check battery usage; reduce brightness; replace battery |
| Random reboots | Software crash, hardware failure | Update OS; factory reset if persistent |
| Bluetooth issues | Pairing failure, interference | Clear paired devices; re-pair |
| Wi-Fi connectivity | DHCP, wrong password, driver | Forget and rejoin; reset network settings |
| NFC not working | Too far away, interfering case | Remove case; hold steady at ~4cm |
| Screen does not autorotate | Rotation lock enabled, accelerometer failure | Check rotation lock; restart; hardware repair |

### 3.3 Mobile OS Security Issues
| Concern | Description | Symptoms |
|---------|-------------|---------|
| Unofficial app stores | Sideloaded apps bypass security review | Unauthorized/malicious apps |
| Developer mode | Enables ADB, debugging; higher risk | Enabled without user knowledge |
| Root access (Android) / Jailbreak (iOS) | Removes OS security restrictions | Voids warranty; bypasses Gatekeeper/Sandbox |
| Unauthorized/malicious app | App installed from untrusted source | Unexpected behavior |
| Application spoofing | Fake app mimicking legitimate one | Looks identical; steals credentials |

**Symptoms of mobile compromise:**
High network traffic, degraded response, data-usage limit notification, limited/no internet, high number of ads, fake security warnings, unexpected app behavior, leaked personal files/data.

### 3.4 PC Security Issues
**Common symptoms of infected PC:**
- Unable to access network (malware blocking)
- Desktop alerts (fake AV warnings)
- False AV protection alerts
- Altered/missing/renamed files
- Inability to access files
- Unwanted OS notifications
- OS update failures (malware blocking updates)

**Browser symptoms:**
- Random/frequent pop-ups → adware or hijacked browser
- Certificate warnings → MITM attack or expired cert
- Browser redirection → DNS hijacking or browser hijack
- Degraded browser performance → malicious extensions or cryptominer

---

## Domain 4 — Operational Procedures (21%)

### 4.1 Documentation and Support Systems
**Ticketing system fields:**
- User info, device info, issue description, category, severity, escalation level
- Written communication: issue description, progress notes, issue resolution

**Asset management:**
| Element | Description |
|---------|-------------|
| Inventory list | All hardware/software assets |
| CMDB | Configuration Management Database; tracks assets and relationships |
| Asset tags/IDs | Physical labels; enables tracking |
| Procurement life cycle | Purchase → deploy → maintain → decommission |
| Warranty and licensing | Track expiration dates |
| Assigned users | Who is responsible for each asset |

**Document types:**
| Document | Purpose |
|----------|---------|
| Incident report | Formal record of security/operational incident |
| SOP | Standard Operating Procedure; step-by-step for recurring tasks |
| New user onboarding checklist | Account creation, hardware setup, access provisioning |
| User offboarding checklist | Account disable, hardware return, access revocation |
| SLA (internal/external) | Service Level Agreement; defines response/resolution times |
| Knowledge base articles | Searchable solutions to known issues |

### 4.2 Change Management
**Change types:**
| Type | Description |
|------|-------------|
| Standard change | Pre-approved; low-risk; routine (e.g., patch Tuesday) |
| Normal change | Requires CAB review and approval |
| Emergency change | Urgent; expedited process; post-review required |

**Change management process:**
1. Submit request form (purpose, scope, change type, date/time, affected systems)
2. Risk analysis (risk level, impact)
3. CAB (Change Advisory Board) approval
4. Sandbox testing / proof of concept
5. Define rollback plan and backup plan
6. Schedule maintenance window (change freeze periods = no changes)
7. Implement with responsible staff members
8. Peer review
9. End-user acceptance testing
10. Document outcomes

**Change freeze:** Period where no changes permitted (e.g., holiday shopping season, fiscal year-end).
**Maintenance window:** Scheduled time for changes; minimizes business impact.

### 4.3 Backup and Recovery
**Backup types:**
| Type | What Backs Up | Archive Bit | Restore |
|------|--------------|-------------|---------|
| Full | Everything | Cleared | Single backup |
| Incremental | Changes since last full or incremental | Cleared | Full + all incrementals in order |
| Differential | Changes since last full | NOT cleared | Full + latest differential only |
| Synthetic full | Combines full + incrementals into new full | — | Single restore |

**Grandfather-Father-Son (GFS) rotation:**
- Son = daily backups (kept ~1 week)
- Father = weekly backups (kept ~1 month)
- Grandfather = monthly backups (kept ~1 year)

**3-2-1 rule:** 3 copies, 2 different media types, 1 offsite or cloud.

**Recovery methods:** In-place/overwrite (restore to same location), alternative location (restore to different path or system).

**Backup testing:** Test restores regularly; a backup never tested is unreliable.

### 4.4 Safety Procedures
- **ESD strap:** Ground yourself before touching components; connect to chassis
- **ESD mat:** Work surface that dissipates static
- **Antistatic bags:** Transport and store components
- **Equipment grounding:** Connect chassis to electrical ground
- **Disconnect power before repair:** Always unplug desktops before opening
- **Lifting techniques:** Lift with legs; keep load close to body; use two people for heavy items
- **Fire safety:** Know location of extinguisher; Class C for electrical fires
- **Safety goggles:** Use when working with pressurized air or chemicals
- **Air filter mask:** Use when cleaning dusty equipment
- **Cable management:** Use velcro ties (not zip ties for permanent runs); prevents airflow blockage

### 4.5 Environmental Controls
- **SDS (Safety Data Sheet):** Required documentation for hazardous materials handling and disposal
- **Battery disposal:** Li-Ion = certified recycler; do not landfill; do not puncture
- **Toner disposal:** Toner cartridges = manufacturer take-back or certified recycler; do not vacuum (use toner-rated vacuum)
- **Temperature/humidity:** Server rooms 64–77°F (18–25°C); 45–55% relative humidity
- **Hot/cold aisle:** Data center layout; alternating cold intake and hot exhaust aisles
- **Dust cleanup:** Compressed air (short bursts); toner-rated vacuum for toner spills
- **UPS (Uninterruptible Power Supply):** Battery backup; keeps systems running during outage; provides clean power
- **Surge suppressor:** Protects against voltage spikes; does NOT provide battery backup

**Power issues:**
| Issue | Description |
|-------|-------------|
| Surge | Brief overvoltage spike |
| Brownout | Sustained undervoltage |
| Blackout | Complete power loss |

### 4.6 Privacy, Licensing, and Policy

**Incident response steps:**
1. Identify and document the incident
2. Chain of custody — document evidence handling; who had access and when
3. Inform management and/or law enforcement as necessary
4. Create forensic copy of drive (preserve data integrity; do not work on original)
5. Document all actions taken
6. Order of volatility — collect evidence from most volatile to least: RAM → cache → running processes → disk → logs → archived media

**Licensing types:**
| License | Description |
|---------|-------------|
| Perpetual | One-time purchase; use forever (no subscription) |
| Personal-use | Single user; may restrict commercial use |
| Corporate/enterprise | Volume; covers multiple users/devices |
| Open-source | Source code available; varies by license (GPL, MIT, Apache) |
| EULA | End-User License Agreement; terms of use; must accept to install |
| DRM | Digital Rights Management; technical enforcement of license restrictions |

**Regulated data categories:**
| Category | Example | Regulation |
|----------|---------|-----------|
| PII | Name, SSN, address, email | GDPR, various state laws |
| Credit card payment info | Cardholder data | PCI-DSS |
| Government-issued ID | Driver's license, passport numbers | State/federal law |
| Healthcare data | Medical records, diagnoses | HIPAA |

**Data retention requirements:** Laws/regulations specify minimum retention periods; must securely destroy after period ends.

**AUP (Acceptable Use Policy):** Defines permitted use of company systems; users must acknowledge.

**NDA/MNDA:** Legal agreement to protect confidential information; mutual NDA = both parties protected.

**Compliance:** Splash screens may be required to present legal notices at login.

### 4.7 Professionalism and Communication
- **Professional appearance:** Match environment (formal or business casual)
- **Proper language:** Avoid jargon, acronyms, slang; speak at user's level
- **Positive attitude:** Project confidence; maintain composure
- **Active listening:** Let customer finish; do not interrupt; restate to confirm understanding
- **Cultural sensitivity:** Use appropriate titles; be aware of cultural differences
- **Punctuality:** Be on time; contact customer if late
- **Avoid distractions:** No personal calls, texting, social media during service call
- **Difficult customers:** Do not argue; do not be defensive; avoid dismissing issues; avoid being judgmental; ask open-ended questions; use discretion
- **Set expectations:** Provide realistic timeline; communicate status updates
- **Documentation:** Provide service documentation; follow up to verify satisfaction
- **Confidentiality:** Do not access or discuss customer data beyond what is needed

### 4.8 Scripting Basics
**Script file types:**
| Extension | Language | Platform |
|-----------|---------|---------|
| .bat | Batch | Windows CMD |
| .ps1 | PowerShell | Windows/cross-platform |
| .vbs | VBScript | Windows legacy |
| .sh | Shell script | Linux/macOS |
| .js | JavaScript | Cross-platform (Node.js) |
| .py | Python | Cross-platform |

**Use cases:** Basic automation, restarting machines, remapping network drives, installing applications, automated backups, gathering data/inventory, initiating updates.

**Risks:** Unintentionally introducing malware (running untrusted scripts), inadvertently changing system settings, browser/system crashes from poorly written scripts.

### 4.9 Remote Access Technologies
| Tool | Protocol/Port | Notes |
|------|--------------|-------|
| RDP | TCP 3389 | Windows; requires Pro/Enterprise; NLA recommended |
| VPN | Various | Encrypted tunnel; split or full tunnel |
| VNC | TCP 5900 | Cross-platform; less secure than RDP; use with caution |
| SSH | TCP 22 | Secure CLI; Linux/macOS/WSL; key-based auth preferred |
| RMM | Varies | Remote Monitoring and Management; agent-based; enterprise |
| SPICE | TCP 5900+ | Simple Protocol for Independent Computing Environments; used with VMs |
| WinRM | TCP 5985/5986 | Windows Remote Management; PowerShell Remoting |
| Screen sharing | HTTPS | Teams, Zoom, TeamViewer; no firewall config needed |
| Desktop management | Varies | TeamViewer, AnyDesk; cloud-relayed |

**Security considerations per method:**
- RDP: Enable NLA; restrict via firewall; use non-default port; enable MFA
- VPN: Use strong auth; keep client software updated
- VNC: Encrypt with SSH tunnel; restrict by IP
- SSH: Use key-based auth; disable password auth; disable root login
- RMM: Verify agent authenticity; restrict agent permissions; audit access logs
- Third-party tools (TeamViewer): Verify connection ID/password; end session when done

### 4.10 Artificial Intelligence (AI) Concepts — NEW in V15

**Application integration:**
- AI tools integrated into productivity apps (Microsoft Copilot in M365, Google Duet in Workspace)
- AI-assisted code completion, email drafting, document summarization
- Chatbots and virtual assistants in helpdesk/support workflows

**Policy considerations:**
| Concern | Description |
|---------|-------------|
| Appropriate use | Define what tasks AI tools may/may not be used for |
| Plagiarism | AI-generated content may violate academic/professional integrity policies |
| Data security | Submitting company data to public AI tools may violate data policies |

**AI limitations:**
| Limitation | Description |
|------------|-------------|
| Bias | AI reflects biases in training data; may produce discriminatory outputs |
| Hallucinations | AI confidently generates false information; must verify AI outputs |
| Accuracy | AI is not always correct; not a substitute for authoritative sources |

**Private vs public AI:**
| Type | Description | Risk |
|------|-------------|------|
| Public AI | ChatGPT, Claude, Gemini; data sent to vendor | Data privacy concern; may train on inputs |
| Private AI | On-premises or private cloud deployment | Higher cost; data stays internal |
| Data source | Training data quality affects output quality | Garbage in, garbage out |
| Data privacy | Do not submit PII, PHI, PCI data to public AI tools | Compliance violation |

---

## Quick Reference — Core 2 Exam Gotchas

- **Core 2 passing score: 700** (Core 1: 675)
- **Malware removal: 10 steps** — memorize the exact order; step 3 = disable System Restore; step 7 = reimage if needed
- **NTFS + Share permissions:** Most restrictive wins for network access; local access = NTFS only
- **UAC:** Never disable on shared/corporate machines
- **BitLocker needs TPM 2.0** (or USB key workaround via gpedit)
- **EFS:** Tied to user certificate — export cert before wiping
- **Chain of custody:** Document who touched evidence and when
- **Order of volatility:** RAM → cache → processes → disk → logs → archive
- **WPA3 uses SAE** (Simultaneous Authentication of Equals) — replaces PSK handshake
- **RADIUS uses UDP 1812/1813**; TACACS+ uses TCP; TACACS+ encrypts full payload
- **Kerberos = port 88**; ticket-based; used in Windows AD
- **3-2-1 backup rule:** 3 copies, 2 media types, 1 offsite
- **Incremental:** Faster backup, slower restore (need full + all incrementals)
- **Differential:** Slower backup, faster restore (need full + latest diff only)
- **GFS rotation:** Son (daily) → Father (weekly) → Grandfather (monthly)
- **Standard change:** Pre-approved; no CAB needed
- **Emergency change:** Expedited; CAB review after the fact
- **Change freeze:** No changes allowed during defined period
- **SDS (Safety Data Sheet):** Required for hazardous materials; replaces old MSDS term
- **EOL system:** No security updates; must upgrade or isolate
- **SSD data destruction:** Overwrite not reliable; use manufacturer secure erase or physical destruction
- **Zero Trust:** Never trust, always verify — even inside the network
- **PAM:** Controls privileged accounts; just-in-time access
- **AI hallucinations:** AI confidently produces false information; always verify
- **Do not submit PII/PHI/PCI to public AI tools** — compliance violation
- **QR code phishing:** New in V15; scanning malicious QR codes redirects to phishing site
- **Supply chain attack:** Compromise the software build/update pipeline (SolarWinds example)
- **BEC (Business Email Compromise):** Impersonate exec via email; authorize fraudulent transfers
- **Boot sector virus:** Infects MBR; runs before OS loads; particularly nasty to remove

---

## Related Notes
- CompTIA-Aplus-Core1-220-1201
- Security-Plus-SY0-701
- Malware-Analysis
- Windows-Commands
- Linux-Commands
- PowerShell-Security

## References
- CompTIA A+ Core 2 (220-1202) V15 Official Exam Objectives
- Professor Messer A+ Course Notes: https://www.professormesser.com
- Mike Meyers CompTIA A+ Certification Study Guide

## See Also
- CompTIA-Aplus-Core1-220-1201
- Security-Plus-SY0-701
- Windows-Commands
- PowerShell-Security

---

## Exam Focus Callouts — Core 2

> [!important] MUST MEMORIZE — Malware Removal (10 Steps, Exact Order)
> 1. ==Investigate and verify malware symptoms==
> 2. ==Quarantine the infected system== (disconnect from network)
> 3. ==Disable System Restore== in Windows Home
> 4. ==Remediate infected systems==
> 5. ==Update anti-malware software==
> 6. ==Scan and removal techniques== (Safe Mode / preinstallation environment)
> 7. ==Reimage/reinstall== if remediation fails
> 8. ==Schedule scans and run updates==
> 9. ==Enable System Restore and create restore point==
> 10. ==Educate the end user==
> - CompTIA tests both the order AND the specific step numbers — memorize all 10

> [!important] MUST MEMORIZE — Backup Types
> | Type | What It Backs Up | Clears Archive Bit | Restore Complexity |
> |------|-----------------|--------------------|--------------------|
> | ==Full== | Everything | Yes | Simplest — 1 backup |
> | ==Incremental== | Changes since last full **or incremental** | Yes | Complex — full + **all** incrementals in order |
> | ==Differential== | Changes since last full only | **No** | Moderate — full + latest differential only |
> | ==Synthetic full== | Combines full + incrementals into new full | — | Same as full |
> - ==Incremental:== faster backup, slower restore
> - ==Differential:== slower backup, faster restore
> - **3-2-1 rule:** 3 copies, 2 media types, 1 offsite
> - **GFS rotation:** Son (daily) → Father (weekly) → Grandfather (monthly)

> [!important] MUST MEMORIZE — Wireless Security Protocols
> | Protocol | Encryption | Status |
> |----------|-----------|--------|
> | ==WEP== | RC4 | **Broken — never use** |
> | ==WPA== | TKIP/RC4 | Deprecated |
> | ==WPA2-Personal== | AES-CCMP | Minimum acceptable |
> | ==WPA2-Enterprise== | AES-CCMP + 802.1X | Corporate standard |
> | ==WPA3-Personal== | SAE | Current best |
> | ==WPA3-Enterprise== | AES-256-GCMP + 802.1X | Highest security |
> - ==RADIUS== = UDP 1812/1813; centralized 802.1X auth
> - ==TACACS+== = TCP; Cisco; encrypts **entire** payload (RADIUS only encrypts password)
> - ==Kerberos== = port 88; ticket-based; Windows AD SSO

> [!important] MUST MEMORIZE — NTFS vs Share Permissions
> - ==Local access== → only NTFS permissions apply (share permissions irrelevant)
> - ==Network access== → most restrictive of NTFS **AND** Share wins
> - ==Inheritance== → child objects inherit parent permissions by default
> - ==Run as Administrator== → UAC prompt; allows elevation without full admin login
> - ==Never disable UAC== on shared or corporate machines

> [!important] MUST MEMORIZE — Windows Editions Feature Matrix
> | Feature | Home | Pro | Enterprise |
> |---------|------|-----|-----------|
> | ==Domain join== | No | **Yes** | Yes |
> | ==BitLocker== | No | **Yes** | Yes |
> | ==Hyper-V== | No | **Yes** | Yes |
> | ==gpedit.msc== | No | **Yes** | Yes |
> | ==RDP (host)== | No | **Yes** | Yes |
> - ==Windows 11 requires:== TPM 2.0 + Secure Boot + 8th gen Intel / Ryzen 2000+
> - ==GPT required for:== UEFI boot, disks >2TB, Windows 11

> [!tip] NEW V15 CONTENT — AI Concepts (4.10) — Guaranteed Exam Questions
> - ==Hallucinations== = AI confidently produces **false information** — always verify AI outputs
> - ==Bias== = AI reflects biases in training data; may produce discriminatory outputs
> - ==Accuracy== = AI is not always correct; not a substitute for authoritative sources
> - ==Public AI== (ChatGPT, Claude, Gemini) = data sent to vendor = **data privacy risk**
> - ==Private AI== = on-prem or private cloud = data stays internal = higher cost
> - **Do NOT submit PII, PHI, or PCI data to public AI tools** — compliance violation
> - ==Plagiarism concern== = AI-generated content may violate academic/professional integrity policy
> - ==Appropriate use policy== = organizations must define what AI tools may/may not be used for

> [!tip] EXAM TRAPS — Common Wrong Answer Choices (Core 2)
> - ==Core 2 passing score: 700== (Core 1 is 675 — different scores)
> - ==Malware step 3== = disable System Restore (not "run a scan" — that's step 6)
> - ==SDS== = Safety Data Sheet (old term was MSDS — exam may use either)
> - ==Chain of custody== = document who touched evidence and when
> - ==Order of volatility== = RAM → cache → processes → disk → logs → archive
> - ==SSO== = one credential for multiple systems; ==SAML== = the protocol that enables it
> - ==Zero Trust== = never trust, always verify — even inside the network perimeter
> - ==PAM== = Privileged Access Management — controls admin/elevated accounts
> - ==EFS== tied to user certificate — export cert before wiping/resetting
> - ==BitLocker== requires TPM 2.0 (or USB key workaround via gpedit)
> - ==Standard change== = pre-approved; no CAB needed
> - ==Emergency change== = expedited; CAB review happens AFTER
> - ==Change freeze== = no changes permitted during defined period (e.g., holidays)
> - ==QR code phishing== = NEW threat type in V15 — scanning malicious QR codes
> - ==Supply chain attack== = compromise the build/update pipeline (SolarWinds)
> - ==BEC== = Business Email Compromise = impersonate exec; authorize fake wire transfer
> - ==Boot sector virus== = infects MBR; runs before OS loads; hardest to detect/remove

> [!warning] HIGH-FREQUENCY EXAM TOPICS — Core 2
> These appear repeatedly across CompTIA A+ practice exams:
> 1. ==Malware removal 10-step process== — order matters; steps 3, 6, 7, 9 most tested
> 2. ==Backup types== — incremental vs differential restore complexity
> 3. ==NTFS permissions== — local vs network; inheritance; effective permissions
> 4. ==Windows editions== — which features are Pro-only (BitLocker, Hyper-V, gpedit, domain)
> 5. ==Wireless security protocols== — WEP=broken; WPA3=current; RADIUS vs TACACS+
> 6. ==Social engineering attacks== — phishing types; QR code phishing (new V15)
> 7. ==Change management types== — standard/normal/emergency; what requires CAB
> 8. ==AI concepts== — hallucinations, bias, public vs private, data privacy (new V15)
> 9. ==Data destruction== — SSD cannot be reliably overwritten; degaussing kills the drive too
> 10. ==Incident response== — chain of custody; order of volatility; forensic copy of drive
> 11. ==Linux config files== — /etc/passwd, /etc/shadow, /etc/fstab, /etc/resolv.conf
> 12. ==macOS features== — FileVault, Time Machine, Gatekeeper, Keychain, Force Quit

---
<- [[Certs-MOC]]