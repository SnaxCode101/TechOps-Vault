---
title: CTF Forensics Techniques
tags:
  - intermediate
  - CTF
  - forensics
  - PCAP
  - memory
  - disk
  - steganography
  - DFIR
  - Wireshark
  - Volatility
topic: Forensics CTF Deep Dive
difficulty: intermediate
created: 2026-03-28
updated: 2026-03-28
---

# 🔬 CTF Forensics Techniques

## Overview
Forensics challenges require extracting hidden data from files, network captures, memory dumps, and disk images. This note covers systematic approaches for each forensics subcategory with tool commands and detection patterns.

---

## Network Forensics (PCAP Analysis)

### Initial Triage
```bash
# Get protocol statistics
tshark -r capture.pcap -z io,phs

# List conversations
tshark -r capture.pcap -z conv,tcp
tshark -r capture.pcap -z conv,ip

# Export HTTP objects
tshark -r capture.pcap --export-objects http,./exported_http/

# Quick string search for flags
strings capture.pcap | grep -iE "(flag|ctf)\{[^}]+\}"
tshark -r capture.pcap -Y "frame contains \"flag{\"" -T fields -e data
```

### Wireshark Display Filters
```
# Protocol filters
http
dns
ftp
ftp-data
smtp
tcp.stream eq 0
tcp.port == 4444

# Credential hunting
http.authbasic
http contains "password"
http contains "login"
ftp.request.command == "PASS"
smtp contains "AUTH"

# Data exfiltration indicators
dns.qry.name.len > 50                    # DNS tunneling
http.request.uri contains "base64"       # Encoded data in URL
icmp.data.len > 48                       # ICMP tunneling
tcp.len > 1000 && tcp.dstport == 53      # DNS over TCP (large)

# File transfer detection
ftp-data
http.content_type contains "application"
http.response.code == 200 && http.content_length > 10000
```

### Protocol-Specific Extraction

**HTTP:**
```bash
# Extract all files transferred via HTTP
tshark -r capture.pcap --export-objects http,./http_files/
# Or: Wireshark → File → Export Objects → HTTP
```

**FTP:**
```bash
# Follow FTP data streams
# Filter: ftp-data → right-click → Follow TCP Stream → Save As
# Credentials: filter ftp.request.command == "USER" || ftp.request.command == "PASS"
```

**DNS Tunneling:**
```bash
# Extract encoded data from DNS queries
tshark -r capture.pcap -Y "dns.qry.type == 1" -T fields -e dns.qry.name | \
    sed 's/\.evil\.com$//' | tr -d '\n' | base64 -d
```

**SMTP / Email:**
```bash
# Follow SMTP stream → look for base64-encoded attachments
# Filter: smtp → Follow TCP Stream
# Decode attachment: copy base64 block → base64 -d > attachment
```

**TLS/SSL:**
```bash
# If you have the private key or session keys:
# Wireshark → Edit → Preferences → Protocols → TLS → RSA keys list
# Or: set (Pre)-Master-Secret log filename
```

---

## Memory Forensics

### Volatility 2 Workflow
```bash
# Step 1: Identify profile (OS version)
vol.py -f memory.dmp imageinfo

# Step 2: List processes
vol.py -f memory.dmp --profile=Win7SP1x64 pslist
vol.py -f memory.dmp --profile=Win7SP1x64 pstree
vol.py -f memory.dmp --profile=Win7SP1x64 psscan    # Hidden processes

# Step 3: Network connections
vol.py -f memory.dmp --profile=Win7SP1x64 netscan
vol.py -f memory.dmp --profile=Win7SP1x64 connections  # XP/2003
vol.py -f memory.dmp --profile=Win7SP1x64 connscan

# Step 4: Command history
vol.py -f memory.dmp --profile=Win7SP1x64 cmdscan
vol.py -f memory.dmp --profile=Win7SP1x64 consoles
vol.py -f memory.dmp --profile=Win7SP1x64 cmdline

# Step 5: Files
vol.py -f memory.dmp --profile=Win7SP1x64 filescan | grep -i "flag\|secret\|password\|desktop"
vol.py -f memory.dmp --profile=Win7SP1x64 dumpfiles -Q <OFFSET> -D ./dumped/

# Step 6: Registry
vol.py -f memory.dmp --profile=Win7SP1x64 hivelist
vol.py -f memory.dmp --profile=Win7SP1x64 hashdump   # SAM hashes

# Step 7: Malware detection
vol.py -f memory.dmp --profile=Win7SP1x64 malfind
vol.py -f memory.dmp --profile=Win7SP1x64 apihooks
vol.py -f memory.dmp --profile=Win7SP1x64 dlllist -p <PID>

# Step 8: Strings search
strings -a memory.dmp | grep -i "flag{"
vol.py -f memory.dmp --profile=Win7SP1x64 yarascan --yara-rules="flag{"
```

### Volatility 3 Workflow
```bash
# No profile needed — auto-detection
vol3 -f memory.dmp windows.info
vol3 -f memory.dmp windows.pslist
vol3 -f memory.dmp windows.pstree
vol3 -f memory.dmp windows.netscan
vol3 -f memory.dmp windows.cmdline
vol3 -f memory.dmp windows.filescan
vol3 -f memory.dmp windows.dumpfiles --physaddr <OFFSET>
vol3 -f memory.dmp windows.hashdump
vol3 -f memory.dmp windows.malfind

# Linux memory
vol3 -f memory.dmp linux.pslist
vol3 -f memory.dmp linux.bash
```

---

## Disk Forensics

### Image Mounting
```bash
# Mount disk image read-only
sudo mount -o ro,loop disk.img /mnt/evidence

# For partition images, find offset first
fdisk -l disk.img
# offset = start_sector × 512
sudo mount -o ro,loop,offset=1048576 disk.img /mnt/evidence

# EWF (EnCase) format
ewfmount image.E01 /mnt/ewf/
sudo mount -o ro,loop /mnt/ewf/ewf1 /mnt/evidence
```

### File Recovery / Carving
```bash
# foremost — recover deleted files by header/footer signatures
foremost -i disk.img -o ./recovered/

# scalpel — configurable file carving
scalpel -c /etc/scalpel/scalpel.conf -o ./carved/ disk.img

# photorec — interactive file recovery
photorec disk.img

# testdisk — partition/file recovery
testdisk disk.img
```

### Timeline Analysis
```bash
# Autopsy GUI — timeline, keyword search, hash analysis
autopsy

# fls + mactime (Sleuth Kit)
fls -r -m "/" disk.img > bodyfile.txt
mactime -b bodyfile.txt -d > timeline.csv
```

### Interesting Locations
```
# Linux
/etc/passwd, /etc/shadow
/home/*/.bash_history
/home/*/.ssh/
/var/log/auth.log, /var/log/syslog
/tmp/, /var/tmp/
/root/
Deleted files in unallocated space

# Windows
C:\Users\*\AppData\
C:\Users\*\Desktop\
C:\Users\*\Documents\
C:\Windows\System32\config\ (SAM, SYSTEM, SECURITY)
$Recycle.Bin
$MFT (Master File Table)
Prefetch files (C:\Windows\Prefetch\)
Event logs (C:\Windows\System32\winevt\Logs\)
```

---

## Steganography Deep Dive

### Image Steganography

**Metadata First:**
```bash
exiftool image.jpg      # Check all metadata fields — comments, GPS, author
identify -verbose image.png   # ImageMagick detailed info
```

**Embedded Files:**
```bash
binwalk image.png       # Check for appended files
binwalk -e image.png    # Extract them
foremost image.png      # Alternate carver

# Manual: check file size vs expected size
# If image file is larger than pixels would require → data appended
```

**LSB Steganography:**
```bash
# PNG/BMP
zsteg image.png                  # Try all LSB combinations
zsteg image.png -a               # All methods

# JPEG (lossy — different tools)
steghide info image.jpg
steghide extract -sf image.jpg              # No password
steghide extract -sf image.jpg -p ""        # Empty password
steghide extract -sf image.jpg -p password  # With password

# stegseek — fast steghide password cracker
stegseek image.jpg /usr/share/wordlists/rockyou.txt

# stegsolve (Java GUI)
java -jar stegsolve.jar
# Try: Red/Green/Blue plane 0, XOR, combine planes
```

**Pixel Manipulation:**
```python
from PIL import Image

img = Image.open('image.png')
pixels = list(img.getdata())

# Extract LSB of each color channel
bits = ''
for pixel in pixels:
    for channel in pixel[:3]:  # R, G, B
        bits += str(channel & 1)

# Convert bits to bytes
flag = bytes(int(bits[i:i+8], 2) for i in range(0, len(bits), 8))
print(flag)
```

### Audio Steganography
```bash
# Spectrogram analysis
# Open in Audacity → Select all → Analyze → Plot Spectrum
# Or: switch to Spectrogram view (dropdown on track header)

# Sonic Visualiser — more precise spectrogram
# Look for: text, QR codes, images hidden in frequency domain

# LSB in WAV
python3 -c "
import wave
w = wave.open('audio.wav', 'rb')
frames = w.readframes(w.getnframes())
bits = ''.join(str(b & 1) for b in frames)
text = bytes(int(bits[i:i+8], 2) for i in range(0, len(bits), 8))
print(text[:200])
"

# DTMF tones (phone dial tones)
# Tool: multimon-ng
multimon-ng -t wav audio.wav -a DTMF
```

### Document Steganography
```bash
# PDF
pdf-parser.py suspicious.pdf                    # View objects
pdf-parser.py -o 5 suspicious.pdf               # Dump specific object
pdfextract suspicious.pdf                       # Extract all embedded
strings suspicious.pdf | grep -i flag

# Office documents (docx/xlsx/pptx = ZIP archives)
unzip document.docx -d ./extracted/
# Check: word/document.xml, [Content_Types].xml, custom XML parts
# Hidden text: white-on-white, font size 1, hidden paragraph property
```

---

## File Analysis Workflow

```
1. file <target>           → identify true file type
2. xxd <target> | head     → check magic bytes match extension
3. strings <target>        → quick flag/password search
4. exiftool <target>       → metadata (author, GPS, comments, timestamps)
5. binwalk <target>        → embedded files or appended data
6. entropy analysis        → high entropy = encrypted/compressed
7. Type-specific tools     → see sections above
```

### Entropy Analysis
```bash
# binwalk entropy plot
binwalk -E file

# High entropy (>7.5 for 8-bit) = likely encrypted or compressed
# Flat high entropy throughout = encryption
# Variable entropy = mixed content (headers + compressed data)
```

---

## Related Notes
- CTF-Methodology — General CTF approach
- CTF-Quick-Reference — Rapid decode/identify reference
- Digital-Forensics — DFIR fundamentals
- Volatility — Memory forensics tool reference
- Wireshark — Packet analysis reference
- Autopsy — Disk forensics GUI

---
*Created: 2026-03-28*
---
<- [[CTF-MOC]]