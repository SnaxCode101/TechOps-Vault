---
title: Autopsy
tags: |-
  - tool
  - autopsy
  - forensics
  - DFIR
  - disk-forensics
  - Sleuth-Kit
  - intermediate
category: forensics
platform: cross-platform (Windows primary)
install: https://www.autopsy.com/download/
created: 2026-03-26
updated: 2026-03-26
difficulty: intermediate
---

# 🔬 Autopsy

## What It Is
Autopsy is an open-source, GUI-based digital forensics platform built on The Sleuth Kit (TSK). It is one of the most widely used forensics tools for disk image analysis, timeline reconstruction, keyword search, and evidence report generation. Used by law enforcement, DFIR analysts, and security researchers.

## Installation
```bash
# Windows (recommended — full feature set)
# Download installer: https://www.autopsy.com/download/

# Linux (limited — most features work on Windows)
sudo apt install autopsy   # Older version
# Or: download .deb from GitHub releases

# Verify
autopsy --version
```

---

## Key Features

| Feature | Description |
|---|---|
| Disk image analysis | Analyze E01, dd, VMDK, raw images |
| File system analysis | NTFS, FAT32, exFAT, EXT, HFS+ support |
| Keyword search | Grep-style search across all extracted content |
| File carving | Recover deleted files |
| Hash calculation | MD5/SHA-256 all artifacts |
| Timeline analysis | Chronological view of all file system events |
| Web artifact analysis | Browser history, cookies, bookmarks |
| Email analysis | PST, Outlook, Thunderbird |
| Registry analysis | Windows registry hive parsing |
| EXIF metadata | Photo/document metadata extraction |
| Hash set filtering | NSRL hash database to exclude known-good files |
| Reporting | HTML/Excel/PDF evidence reports |

---

## Creating a Case in Autopsy

```
1. Open Autopsy → New Case
2. Enter Case Name, Case Number, Examiner
3. Add Data Source:
   - Disk Image or VM File (.E01, .dd, .vmdk)
   - Local Disk
   - Logical Files (individual files/folders)
4. Select Ingest Modules (what to automatically analyze):
   ✅ Hash Lookup (NSRL)
   ✅ Keyword Search
   ✅ File Type Identification
   ✅ Exif Parser
   ✅ Email Parser
   ✅ Recent Activity (browser, registry)
   ✅ Data Artifact Extractor
5. Click Finish → Analysis begins automatically
```

---

## Key Analysis Areas

### File Analysis
```
Left Panel → Data Sources → [image] → File Views
  - By File Type  → Images, Video, Audio, Documents, Executables
  - Deleted Files → Files recovered from unallocated space
  - By Size, Extension, MIME type
```

### Keyword Search
```
Tools → Keyword Search
  - Lists, Regex searches, Single Terms
  - Pre-built lists: Credit Card Numbers, IP Addresses, URLs, Email Addresses
  - Custom: add your IOCs (domain names, file hashes, strings)
```

### Timeline Analysis
```
Tools → Timeline
  - Visualize all events (file create/modify/access/delete)
  - Filter by date range, event type
  - Zoom to investigate specific timeframes
  - Correlate user activity with events
```

### Web Artifacts
```
Results → Extracted Content → Web History
  - Browser history from Chrome, Firefox, IE/Edge
  - Cookies, downloads, form data, bookmarks
```

### Registry Analysis
```
Results → Extracted Content → Recent Documents
Results → Extracted Content → Run Programs
OS Account information
Recent programs: NTUSER.DAT analysis
```

### Communications / Email
```
Results → Extracted Content → Messages
  - Email messages from PST files, Outlook, Thunderbird
```

---

## Hash Sets (NSRL)

NSRL (National Software Reference Library) — NIST maintains a database of known-good file hashes. Filter these out to focus on unknown/suspicious files.

```
Tools → Options → Hash Database → Import NSRL hash sets
# Download NSRL from: https://www.nist.gov/itl/ssd/software-quality-group/national-software-reference-library-nsrl
```

---

## Tagging and Reporting

```
Right-click any file/artifact → Add Tag → Select category
  Tags: Notable, Bookmark, Bad, Unknown, etc.

Generate Report:
Tools → Generate Report
  - HTML (default) — includes all tagged items + screenshots
  - Excel
  - Stix
```

---

## The Sleuth Kit (TSK) — CLI Companion

Autopsy is a GUI frontend for TSK. Use TSK CLI for automation or scripting:

```bash
# Get disk image information
mmls disk.img                          # Partition layout
fsstat -o [offset] disk.img            # File system info

# List files in an image
fls -o [offset] disk.img               # List files (inode format)
fls -o [offset] -r disk.img            # Recursive

# Read a specific file by inode
icat -o [offset] disk.img [inode]      # Output file content

# Recover deleted files
fls -o [offset] -d disk.img            # List deleted files
icat -o [offset] disk.img [del_inode] > recovered_file

# Timeline
fls -o [offset] -m / -r disk.img > bodyfile.txt
mactime -b bodyfile.txt -d > timeline.csv
```

---

## Autopsy in CTF / THM / HTB Challenges

Common tasks in CTF forensics:
```
1. Import .dd or .E01 image
2. Run all ingest modules
3. Check Timeline for suspicious activity
4. Keyword search for: "flag", "CTF", "password", specific domains
5. Look in Deleted Files for hidden content
6. Check browser history for visited sites
7. Check Recent Activity for recently run programs
8. Extract EXIF from images (hidden GPS, author metadata)
9. Check for steganography: look for unusual image files
```

---

## Related Notes
- Digital-Forensics
- Volatility
- Incident-Response
- Malware-Analysis

## References
- Autopsy official: https://www.autopsy.com
- The Sleuth Kit: https://www.sleuthkit.org
- Autopsy User Documentation: https://sleuthkit.org/autopsy/docs/user-docs/

---
<- [[Tools-MOC]]