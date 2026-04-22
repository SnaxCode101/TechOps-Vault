---
title: Windows Commands Cheat Sheet
tags:
  - reference
  - cheat-sheet
  - windows
  - commands
  - cmd
  - powershell
  - sysadmin
  - foundational
difficulty: foundational
created: 2026-03-23
updated: 2026-03-28
---

# Windows Commands Cheat Sheet

CMD and PowerShell commands for system administration, security operations, and penetration testing.

---

## System Information

```cmd
systeminfo                        :: Full OS, patch, and hardware info
hostname                          :: Machine name
whoami                            :: Current user
whoami /priv                      :: Current user's privileges
whoami /groups                    :: Current user's group memberships
ver                               :: OS version
wmic os get caption,version,buildnumber   :: OS details
```

```powershell
Get-ComputerInfo | Select-Object CsName, WindowsVersion, OsBuildNumber
$env:COMPUTERNAME
[Environment]::OSVersion
Get-WmiObject Win32_OperatingSystem | Select-Object Caption, Version, LastBootUpTime
```

---

## User & Group Management

```cmd
net user                          :: List local users
net user USERNAME                 :: User details
net user USERNAME Password1 /add  :: Create user
net user USERNAME /delete         :: Delete user
net user USERNAME /active:no      :: Disable account
net localgroup                    :: List local groups
net localgroup administrators     :: Group members
net localgroup administrators USERNAME /add    :: Add to group
net localgroup administrators USERNAME /delete :: Remove from group
net accounts                      :: Password policy (min length, lockout, max age)
```

```powershell
Get-LocalUser
Get-LocalUser | Where-Object Enabled -eq $true
New-LocalUser -Name "svcaccount" -Password (ConvertTo-SecureString "P@ss!" -AsPlainText -Force) -PasswordNeverExpires
Set-LocalUser -Name "USERNAME" -PasswordNeverExpires $false
Enable-LocalUser / Disable-LocalUser -Name "USERNAME"
Remove-LocalUser -Name "USERNAME"
Get-LocalGroup
Get-LocalGroupMember -Group "Administrators"
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "USERNAME"
```

---

## Active Directory (Domain Admin Tasks)

```cmd
net user USERNAME /domain              :: Query AD user
net group "Domain Admins" /domain      :: List DA members
net group "GROUP" USERNAME /add /domain :: Add user to AD group
dsquery user -name "john*"             :: Search AD users
dsquery computer -inactive 8           :: Inactive computers (8 weeks)
nltest /dclist:DOMAIN.COM              :: List domain controllers
gpupdate /force                        :: Force Group Policy refresh
gpresult /r                            :: Show applied GPOs
gpresult /h report.html                :: Full GPO report as HTML
```

```powershell
# Requires RSAT / AD module
Import-Module ActiveDirectory
Get-ADUser -Identity USERNAME -Properties *
Get-ADUser -Filter {Enabled -eq $true} | Measure-Object     # Count enabled users
Get-ADUser -Filter {PasswordLastSet -lt (Get-Date).AddDays(-90)}  # Stale passwords
Get-ADGroup -Filter * | Select-Object Name
Get-ADGroupMember -Identity "Domain Admins" -Recursive
Get-ADComputer -Filter * | Select-Object Name, DNSHostName, Enabled
Unlock-ADAccount -Identity USERNAME
Set-ADAccountPassword -Identity USERNAME -Reset -NewPassword (ConvertTo-SecureString "NewP@ss!" -AsPlainText -Force)
Search-ADAccount -LockedOut              # Find locked accounts
Search-ADAccount -AccountDisabled        # Find disabled accounts
Search-ADAccount -PasswordExpired        # Find expired passwords
```

---

## Group Policy

```cmd
gpupdate /force                        :: Refresh policies now
gpresult /r                            :: Resultant Set of Policy (summary)
gpresult /h C:\Temp\gp-report.html     :: Full HTML report
rsop.msc                               :: Resultant Set of Policy GUI
gpedit.msc                             :: Local Group Policy Editor
```

```powershell
# Requires GroupPolicy module (RSAT)
Import-Module GroupPolicy
Get-GPO -All | Select-Object DisplayName, GpoStatus, ModificationTime
Get-GPResultantSetOfPolicy -ReportType Html -Path C:\Temp\rsop.html
Get-GPOReport -All -ReportType HTML -Path C:\Temp\all-gpos.html
# Backup GPO
Backup-GPO -All -Path C:\GPOBackups
```

---

## Network Information

```cmd
ipconfig /all                     :: All interfaces, IP, MAC, DNS
ipconfig /flushdns                :: Flush DNS cache
ipconfig /displaydns              :: View DNS cache
ipconfig /release && ipconfig /renew  :: DHCP release/renew
netstat -ano                      :: Connections + PIDs
netstat -ano | findstr LISTENING  :: Listening ports
route print                       :: Routing table
arp -a                            :: ARP cache
nslookup example.com              :: DNS lookup
nslookup -type=MX example.com    :: MX record
ping -n 4 8.8.8.8                 :: ICMP ping
tracert 8.8.8.8                   :: Traceroute
pathping 8.8.8.8                  :: Combined ping+tracert
net view                          :: Network computers
net share                         :: Local shares
net use Z: \\SERVER\Share /user:DOMAIN\user  :: Map network drive
net use Z: /delete                :: Disconnect mapped drive
```

```powershell
Get-NetIPAddress | Format-Table InterfaceAlias, IPAddress, PrefixLength
Get-NetIPConfiguration
Get-NetAdapter | Select-Object Name, Status, LinkSpeed, MacAddress
Get-NetRoute | Where-Object DestinationPrefix -eq "0.0.0.0/0"  # Default gateway
Get-NetTCPConnection | Where-Object State -eq "Listen" | Sort-Object LocalPort
Get-DnsClientCache
Resolve-DnsName example.com
Resolve-DnsName example.com -Type MX
Test-NetConnection -ComputerName host -Port 80
Test-NetConnection -ComputerName host -TraceRoute
Clear-DnsClientCache
```

---

## Remote Administration

```powershell
# PowerShell Remoting (WinRM — port 5985/5986)
Enter-PSSession -ComputerName SERVER01                        # Interactive session
Enter-PSSession -ComputerName SERVER01 -Credential (Get-Credential)
Exit-PSSession

Invoke-Command -ComputerName SERVER01 -ScriptBlock { Get-Service }  # Run command remotely
Invoke-Command -ComputerName SERVER01,SERVER02 -ScriptBlock { hostname }  # Multi-server
Invoke-Command -ComputerName (Get-Content servers.txt) -ScriptBlock { Get-Service wuauserv }

# Enable WinRM (on target)
Enable-PSRemoting -Force
winrm quickconfig

# Test WinRM connectivity
Test-WSMan -ComputerName SERVER01
```

```cmd
:: Remote Desktop
mstsc /v:SERVER01                  :: Launch RDP to server

:: PsExec (Sysinternals — run commands on remote systems)
psexec \\SERVER01 cmd              :: Remote cmd
psexec \\SERVER01 -u DOMAIN\admin -p pass cmd
```

---

## Service Management

```cmd
sc query                          :: List all services
sc query wuauserv                 :: Specific service status
sc qc wuauserv                    :: Service config (binary path, start type)
sc start / stop / restart ServiceName
net start / net stop ServiceName
sc config ServiceName start=auto  :: Set to auto-start
sc config ServiceName start=disabled
```

```powershell
Get-Service | Where-Object Status -eq "Running"
Get-Service -Name wuauserv | Select-Object *
Start-Service / Stop-Service / Restart-Service -Name wuauserv
Set-Service -Name wuauserv -StartupType Automatic
Get-Service | Where-Object {$_.StartType -eq "Automatic" -and $_.Status -ne "Running"}  # Auto but stopped
```

---

## Event Logs & Monitoring

```powershell
# Get-WinEvent (preferred — replaces Get-EventLog)
Get-WinEvent -LogName Security -MaxEvents 50
Get-WinEvent -LogName System -MaxEvents 20

# Filter by Event ID
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4624} -MaxEvents 10  # Successful logon
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625} -MaxEvents 10  # Failed logon
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4720}                # User created
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4732}                # Added to group
Get-WinEvent -FilterHashtable @{LogName='System'; ID=7045}                  # New service installed
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4688} -MaxEvents 20  # Process creation

# Time-based filter
Get-WinEvent -FilterHashtable @{LogName='Security'; StartTime=(Get-Date).AddHours(-24)}

# Search message content
Get-WinEvent -LogName Security -MaxEvents 1000 | Where-Object {$_.Message -match "administrator"}

# Export to CSV
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625} | Export-Csv C:\Temp\failed-logins.csv
```

```cmd
:: CMD — wevtutil
wevtutil qe Security /c:20 /f:text    :: Last 20 security events
wevtutil el                            :: List all event logs
wevtutil cl Security                   :: Clear security log (⚠️ admin required)
```

### Key Windows Event IDs (Sysadmin Reference)
| Event ID | Log | Meaning |
|---|---|---|
| 4624 | Security | Successful logon |
| 4625 | Security | Failed logon |
| 4648 | Security | Logon with explicit credentials |
| 4672 | Security | Special privileges assigned (admin logon) |
| 4688 | Security | New process created |
| 4698 | Security | Scheduled task created |
| 4720 | Security | User account created |
| 4722 | Security | User account enabled |
| 4725 | Security | User account disabled |
| 4726 | Security | User account deleted |
| 4732 | Security | User added to security group |
| 4740 | Security | Account locked out |
| 7045 | System | New service installed |
| 1074 | System | System shutdown/restart initiated |
| 6005/6006 | System | Event Log service started/stopped |

---

## Windows Update

```powershell
# PSWindowsUpdate module (install: Install-Module PSWindowsUpdate)
Get-WindowsUpdate                      # List available updates
Install-WindowsUpdate -AcceptAll       # Install all
Get-WUHistory | Select-Object -First 20   # Recent update history

# Built-in
Get-HotFix | Sort-Object InstalledOn -Descending | Select-Object -First 10
wmic qfe list brief                    # Installed patches
```

```cmd
:: DISM — servicing and image management
dism /online /get-packages             :: List installed packages
dism /online /cleanup-image /scanhealth    :: Check for corruption
dism /online /cleanup-image /restorehealth :: Repair image from Windows Update
sfc /scannow                           :: System File Checker
```

---

## Disk & Storage

```cmd
diskpart                          :: Interactive disk management
wmic logicaldisk get name,freespace,size  :: Drive space
wmic diskdrive get model,size     :: Physical disks
```

```powershell
Get-Volume | Format-Table DriveLetter, FileSystemLabel, SizeRemaining, Size
Get-Disk | Format-Table Number, FriendlyName, Size, PartitionStyle
Get-PhysicalDisk | Format-Table FriendlyName, MediaType, Size, HealthStatus
Get-Partition
# Check free space percentage
Get-Volume | Where-Object {$_.DriveLetter} | Select-Object DriveLetter, @{N='FreeGB';E={[math]::Round($_.SizeRemaining/1GB,1)}}, @{N='TotalGB';E={[math]::Round($_.Size/1GB,1)}}, @{N='%Free';E={[math]::Round(($_.SizeRemaining/$_.Size)*100,1)}}
```

---

## Scheduled Tasks

```cmd
schtasks /query /fo LIST /v       :: List all tasks
schtasks /create /tn "Backup" /tr "C:\scripts\backup.bat" /sc daily /st 02:00 /ru SYSTEM
schtasks /run /tn "Backup"        :: Run immediately
schtasks /delete /tn "Backup" /f  :: Delete task
```

```powershell
Get-ScheduledTask | Where-Object State -eq "Ready"
Get-ScheduledTask | Where-Object {$_.Principal.UserId -eq "SYSTEM"}
Register-ScheduledTask -TaskName "Cleanup" -Trigger (New-ScheduledTaskTrigger -Daily -At 3am) -Action (New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-File C:\scripts\cleanup.ps1") -RunLevel Highest -User "SYSTEM"
Unregister-ScheduledTask -TaskName "Cleanup" -Confirm:$false
```

---

## File System

```cmd
dir /a                            :: Include hidden files
dir /s /b *.log                   :: Recursive search
type file.txt                     :: Print file
copy / move / del / mkdir / rmdir
icacls file.txt                   :: Show permissions
icacls dir\ /grant user:(OI)(CI)F :: Grant full control
icacls dir\ /remove user          :: Remove permissions
attrib +h / -h file.txt           :: Hide/unhide
where python                      :: Find binary in PATH
```

```powershell
Get-ChildItem -Force -Recurse -Filter "*.log"
Get-Content file.txt
Get-Acl file.txt | Format-List
Set-Acl / icacls                  # Modify ACLs
Test-Path C:\path\file.txt        # Check if exists
```

---

## Backup & Recovery

```cmd
:: Windows Server Backup (wbadmin)
wbadmin start backup -backupTarget:E: -include:C: -allCritical -quiet
wbadmin get versions              :: List backup versions
wbadmin start systemstaterecovery -version:MM/DD/YYYY-HH:MM
```

```powershell
# Windows Server Backup module
Install-WindowsFeature Windows-Server-Backup
Get-WBBackup
```

---

## Firewall

```cmd
netsh advfirewall show allprofiles
netsh advfirewall firewall show rule name=all
netsh advfirewall set allprofiles state off    :: Disable (⚠️)
netsh advfirewall firewall add rule name="AllowSSH" protocol=TCP dir=in localport=22 action=allow
netsh advfirewall firewall delete rule name="AllowSSH"
```

```powershell
Get-NetFirewallRule | Where-Object Enabled -eq "True" | Select-Object DisplayName, Direction, Action
New-NetFirewallRule -DisplayName "Allow RDP" -Direction Inbound -Protocol TCP -LocalPort 3389 -Action Allow
Remove-NetFirewallRule -DisplayName "Allow RDP"
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
```

---

## Windows Defender

```powershell
Get-MpComputerStatus                   # Defender status
Get-MpThreatDetection                  # Recent detections
Update-MpSignature                     # Update definitions
Start-MpScan -ScanType QuickScan      # Quick scan
Start-MpScan -ScanType FullScan       # Full scan
Add-MpPreference -ExclusionPath "C:\Tools"   # Add exclusion
Get-MpPreference | Select-Object Exclusion*  # View exclusions
Set-MpPreference -DisableRealtimeMonitoring $true  # Disable RT (⚠️ admin, testing only)
```

---

## Registry

```cmd
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run   :: Startup items
reg query HKLM /f "password" /t REG_SZ /s                      :: Search for "password"
reg add HKLM\... /v ValueName /t REG_SZ /d "data"
reg delete HKLM\... /v ValueName /f
reg export HKLM\SOFTWARE\MyApp backup.reg                       :: Export
reg import backup.reg                                           :: Import
```

```powershell
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion"
Get-ChildItem "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
Set-ItemProperty -Path "HKLM:\..." -Name "ValueName" -Value "data"
```

---

## Credential & Security Tools

```cmd
cmdkey /list                      :: Stored credentials
netsh wlan show profiles          :: WiFi profiles
netsh wlan show profile name="SSID" key=clear  :: WiFi password
reg save HKLM\SAM sam.hive       :: Dump SAM (admin, for forensics)
reg save HKLM\SYSTEM system.hive
```

---

## LOLBins (Living Off the Land)

| Binary | Abuse |
|---|---|
| `certutil.exe` | Download files, decode base64 |
| `bitsadmin.exe` | Download files, BITS persistence |
| `mshta.exe` | Execute HTA payloads |
| `regsvr32.exe` | Execute DLLs, AppLocker bypass |
| `rundll32.exe` | Execute DLL functions |
| `msiexec.exe` | Install MSI from URL |
| `wmic.exe` | Process creation, lateral movement |
| `powershell.exe` | Encoded commands, download cradles |

Reference: https://lolbas-project.github.io/

---

## Related Notes
- Active-Directory
- Linux-Commands
- Windows-Privilege-Escalation
- PowerShell-Security
- System-Admin-Reference
- Common-Ports

---
*Updated: 2026-03-28 | Difficulty: Foundational*
---
<- [[Reference-MOC]]