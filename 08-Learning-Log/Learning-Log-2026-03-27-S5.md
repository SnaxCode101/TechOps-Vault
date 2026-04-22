---
title: Learning Log — 2026-03-27 Session 5
type: learning-log
created: 2026-03-27
updated: 2026-03-27
tags: |-
  - learning-log
  - session-5
  - labs
  - medical-devices
  - tefca
  - vault-search
---

# 📓 Learning Log — 2026-03-27 (Session 5)

## Session Summary

Continued vault buildout from Session 4 handoff (123 notes). Worked through the full priority list top to bottom — focused on filling gaps in labs, validating cert depth, building the medical devices section, and adding TEFCA/care coordination coverage. Vault grew from 123 → 138 notes.

---

## Work Completed

### High Priority — Vault-Search Rebuild
- **Vault-Search.md** completely rebuilt from 74-note outdated index to full 138-note master index
- Added all missing sections: 09-Epic (12), 10-Healthcare-IT (7), 11-Medical-Devices (6)
- Expanded 05-Certs from 7 → 32 entries with full keyword search terms
- Added OSINT, Wireless, Container-Docker to Fundamentals section
- Added JavaScript Fundamentals, SN JavaScript, SN Navigator, Database Security to Reference
- Updated tag index with healthcare, medical device, and ServiceNow tags

### High Priority — Lab Write-ups (03-Labs/)
- **8 new lab write-ups** added (3 → 11 labs total)
- HTB: Jerry (Tomcat default creds), Nibbles (Nibbleblog CVE), Shocker (Shellshock), Bashed (phpbash + cron), Netmon (PRTG + anon FTP)
- THM: Kenobi (ProFTPd mod_copy + SUID PATH), Ice (Icecast buffer overflow + token impersonation), Vulnversity (upload bypass + systemctl SUID)
- All follow vault format standard: frontmatter → lab info → enumeration → vulnerability background → foothold → privesc → flags → key takeaways → tools used → references

### Medium Priority — ServiceNow Cert Depth Audit
- Verified all 21 ServiceNow cert notes (CSA, CAD, CTA, CMA, 4 micro-certs, 16 CIS certs) — all already at full study-guide depth from Session 4
- No additional work needed — Session 4 was more thorough than handoff indicated

### Medium Priority — 11-Medical-Devices/ Section (NEW)
- Built complete new section with 6 notes:
  - **Medical-Devices-Overview** — section hub, frameworks, quick-access table
  - **FDA-Cybersecurity-Guidance** — premarket/postmarket guidance, SBOM, PATCH Act, MDS2 forms, procurement/deployment checklists
  - **Medical-Device-Network-Security** — VLAN design, micro-segmentation, NAC, passive monitoring, vendor remote access, wireless
  - **IoT-OT-Security-Fundamentals** — Purdue model, protocols (DICOM, Modbus, MQTT, BACnet), OWASP IoT Top 10, MITRE ATT&CK for ICS
  - **Medical-Device-Risk-Management** — IEC 80001-1, IEC 62443, NIST CSF, AAMI TIR57, STRIDE threat modelling, compensating controls
  - **Medical-Device-Incident-Response** — clinical safety assessment, containment decision tree, ransomware scenario, FDA MedWatch reporting

### Medium Priority — TEFCA & Care Coordination
- **TEFCA-Care-Coordination.md** created in 10-Healthcare-IT/
- Covers: TEF/CA framework, QHINs, USCDI v4 data classes, exchange patterns, 21st Century Cures Act, Information Blocking Rule (8 exceptions), ADT notifications, Transition of Care, referral management, SDOH coordination, FHIR/SMART on FHIR integration, security requirements, patient matching challenges

### MOC Updates
- **Dashboard.md** rebuilt 4 times during session (emoji heading patch bug → delete/rewrite workflow)
- **Vault-Search.md** rebuilt 3 times
- All counts verified: 138 notes across 12 sections

---

## Vault State at End of Session 5

| Section | Notes | Change |
|---|---|---|
| 00-MOC | 5 | — |
| 01-Fundamentals | 23 | — |
| 02-Tools | 20 | — |
| 03-Labs | 11 | +8 |
| 04-CTF | 2 | — |
| 05-Certs | 32 | — |
| 06-Reference | 18 | — |
| 07-Projects | 2 | — |
| 08-Learning-Log | 6 | +1 (this entry) |
| 09-Epic | 12 | — |
| 10-Healthcare-IT | 7 | +1 |
| 11-Medical-Devices | 6 | +6 (new section) |
| **Total** | **139** | +16 from session start |

---

## Known Issues

- **Vault-Search.md body** — Healthcare IT section header still says "6 notes" and is missing TEFCA keyword row. Frontmatter is correct (138→139 after this log entry). Fix: delete/rewrite Healthcare IT section heading next session.
- **obsidian_patch_content** — continues to fail on emoji-prefixed headings and headings with parentheses/numbers. Workaround: delete → full rewrite. This is a known MCP API behavior, not a vault issue.

---

## Cert Progress

- ServiceNow Micro-Cert — Welcome ✅ (2026-03-25)
- ServiceNow Micro-Cert — Flows ✅ (2026-03-26)
- ServiceNow Micro-Cert — Platform Analytics ✅ (2026-03-26)
- ServiceNow Micro-Cert — Automated Test Framework ✅ (2026-03-27)
- Working toward: ServiceNow CSA

---

## Next Session Priorities

| Priority | Task |
|---|---|
| Quick fix | Vault-Search.md — update Healthcare IT section (6→7, add TEFCA row) |
| Quick fix | Dashboard/Vault-Search note counts → 139 (after this learning log) |
| Medium | Expand 04-CTF/ — more CTF write-ups and challenges |
| Medium | Expand 03-Labs/ — medium-difficulty boxes (HTB: Optimum, Bastard, Devel; THM: Steel Mountain, Relevant) |
| Medium | Build learning pathway notes — structured study plans for Security+, CSA, eJPT |
| Low | Expand 07-Projects/ — additional project templates and active project tracking |

---
*Session 5 complete: 2026-03-27*

---
<- [[Learning-Log-MOC]]