---
title: Volatility
tags: '["tool", "volatility", "memory-forensics", "DFIR", "RAM",
  "malware-analysis", "advanced"]'
category: forensics
platform: cross-platform (Python)
install: pip install volatility3 / git clone
  https://github.com/volatilityfoundation/volatility3
created: 2026-03-26
updated: 2026-03-26
difficulty: advanced
---

# 🧠 Volatility

## What It Is
Volatility is the leading open-source memory forensics framework. It analyzes RAM dumps from Windows, Linux, and macOS to reconstruct system state at the time of capture: running processes, network connections, loaded DLLs, injected code, encryption keys, and more.

**Volatility 3** (current): Python 3, no profiles needed — uses symbol tables.
**Volatility 2** (legacy): Still used for older systems; requires OS profiles.

## Installation
```bash
# Volatility 3 (recommended)
git clone https://github.com/volatilityfoundation/volatility3
cd volatility3
pip3 install -r requirements.txt

# Or via pip
pip3 install volatility3

# Verify
python3 vol.py -h
```

---

## Acquiring a Memory Image

```bash
# Windows — WinPMEM (free, open-source)
winpmem_mini_x64.exe -o memory.raw

# Windows — DumpIt
DumpIt.exe

# Windows — FTK Imager (GUI): File → Capture Memory Image

# Linux — LiME (Loadable Kernel Module)
sudo insmod lime-$(uname -r).ko "path=/mnt/usb/memory.lime format=lime"
```

---

## Volatility 3 — Core Commands

### Get OS Info
```bash
python3 vol.py -f memory.raw windows.info
python3 vol.py -f memory.raw linux.info
```

### Process Analysis
```bash
# List running processes (flat list)
python3 vol.py -f memory.raw windows.pslist

# Process tree (shows parent-child relationships)
python3 vol.py -f memory.raw windows.pstree

# Find hidden processes (compare pslist vs psscan)
python3 vol.py -f memory.raw windows.psscan    # Scan memory for EPROCESS structures

# Process command line arguments
python3 vol.py -f memory.raw windows.cmdline

# Process environment variables
python3 vol.py -f memory.raw windows.envars

# DLLs loaded by each process
python3 vol.py -f memory.raw windows.dlllist
python3 vol.py -f memory.raw windows.dlllist --pid 1234

# Handles (files, registry keys, etc.) open by process
python3 vol.py -f memory.raw windows.handles --pid 1234
```

### Network Analysis
```bash
# Active and recently closed network connections
python3 vol.py -f memory.raw windows.netstat

# List network connections (older scans)
python3 vol.py -f memory.raw windows.netscan
```

### Malware Detection
```bash
# Find injected code / unusual memory regions (malfind)
# Looks for: PAGE_EXECUTE_READWRITE memory, PE headers in unexpected places
python3 vol.py -f memory.raw windows.malfind

# Dump suspicious process memory regions
python3 vol.py -f memory.raw windows.malfind --dump --dump-dir /output/

# Find DLL injection / process hollowing
python3 vol.py -f memory.raw windows.hollowfind  # (community plugin)

# Look for suspicious parent-child relationships
# e.g. cmd.exe parent = winword.exe → macro execution
python3 vol.py -f memory.raw windows.pstree
```

### File & Registry Analysis
```bash
# List files cached in memory
python3 vol.py -f memory.raw windows.filescan

# Dump a specific file by offset
python3 vol.py -f memory.raw windows.dumpfiles --virtaddr 0xXXXXXXXXXX

# Dump all files in a directory
python3 vol.py -f memory.raw windows.dumpfiles --filter ".exe"

# Registry hives
python3 vol.py -f memory.raw windows.registry.hivelist

# Print registry key
python3 vol.py -f memory.raw windows.registry.printkey --key "SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```

### Credential Extraction
```bash
# Dump hashed passwords from memory (NTLM hashes)
python3 vol.py -f memory.raw windows.hashdump

# Extract cached domain credentials
python3 vol.py -f memory.raw windows.cachedump

# Extract LSA secrets
python3 vol.py -f memory.raw windows.lsadump
```

### Kernel Analysis
```bash
# List kernel modules
python3 vol.py -f memory.raw windows.modules
python3 vol.py -f memory.raw windows.modscan   # Scan for hidden modules

# SSDT (System Service Descriptor Table) hooks — rootkit detection
python3 vol.py -f memory.raw windows.ssdt

# Callbacks — kernel notification routines
python3 vol.py -f memory.raw windows.callbacks
```

### String / Code Analysis
```bash
# Dump a process to disk
python3 vol.py -f memory.raw windows.memmap --pid 1234 --dump --dump-dir /output/

# Extract printable strings from a dump
strings /output/pid.1234.dmp

# Search memory for a string
python3 vol.py -f memory.raw windows.strings --strings-file patterns.txt
```

---

## Volatility 2 (Legacy) Key Differences

```bash
# Identify OS profile (needed in Vol2)
python2 vol.py -f memory.raw imageinfo

# Use profile in all commands
python2 vol.py -f memory.raw --profile=Win7SP1x64 pslist
python2 vol.py -f memory.raw --profile=Win7SP1x64 malfind
python2 vol.py -f memory.raw --profile=Win7SP1x64 netscan
```

---

## Common Investigation Workflow

```
1. Identify OS:         windows.info
2. List processes:      windows.pstree — look for anomalies
3. Check network:       windows.netscan — find C2 connections
4. Find injected code:  windows.malfind — dump suspicious regions
5. Check command lines: windows.cmdline — find encoded PowerShell, unusual args
6. Check persistence:   windows.registry.printkey (Run keys)
7. Extract credentials: windows.hashdump
8. Dump files:          windows.filescan → windows.dumpfiles
9. Strings on dump:     strings + grep for IOCs
```

---

## Suspicious Patterns to Look For

| Pattern | Indicator |
|---|---|
| `svchost.exe` not parented by `services.exe` | Process spoofing |
| `cmd.exe` / `powershell.exe` parented by Office app | Macro execution |
| Process with no name or path | Hidden/injected process |
| `malfind` PAGE_EXECUTE_READWRITE region with MZ header | Process injection |
| Port 4444, 443, 80 on unusual process | Reverse shell / C2 |
| `powershell.exe` with encoded `-EncodedCommand` | Obfuscated execution |
| Connections to TOR exit nodes / unusual IPs | C2 traffic |
| `lsass.exe` memory dumped | Credential dumping |

---

## Related Notes
- Digital-Forensics
- Malware-Analysis
- Incident-Response
- Autopsy

## References
- Volatility 3 GitHub: https://github.com/volatilityfoundation/volatility3
- Volatility documentation: https://volatility3.readthedocs.io
- The Art of Memory Forensics (Ligh, Case, Levy, Walters)

---
<- [[Tools-MOC]]