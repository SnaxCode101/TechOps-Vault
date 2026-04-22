---
title: Home Lab Progress Log
tags:
  - project
  - home-lab
  - progress-log
  - tracker
topic: Projects
difficulty: foundational
created: 2026-04-03
updated: 2026-04-03
source: Personal lab build
---

# Home Lab Progress Log

> Living document — update after each lab session. Track what's built, what's broken, and what's next.
> Setup guide lives in [[Home-Lab-Setup]].

---

## Lab Hardware

| Component | Spec | Notes |
|-----------|------|-------|
| Host machine | | |
| RAM | | |
| Storage | | |
| OS | | |
| Hypervisor | | |

---

## VM Inventory

| VM | OS | IP | Status | Purpose |
|----|----|----|--------|---------|
| | | | | |

**Status key:** 🟢 Running · 🟡 Built / Not running · 🔴 Broken / Needs rebuild · ⬜ Not yet built

---

## Build Checklist

### Core Lab (Attack + Basic Target)
- [ ] Hypervisor installed and configured
- [ ] Host-Only / Internal network created (192.168.56.0/24)
- [ ] Kali Linux VM — installed, updated, tools verified
- [ ] Metasploitable 2 — deployed, accessible from Kali
- [ ] DVWA — running (Docker or standalone)
- [ ] Snapshot baseline taken on all VMs

### Active Directory Lab
- [ ] Windows Server 2022 eval — downloaded
- [ ] Windows Server VM — installed, DC promoted (lab.local)
- [ ] Windows 10 eval × 2 — downloaded
- [ ] WS1 domain-joined
- [ ] WS2 domain-joined
- [ ] Test users created (alice, bob)
- [ ] Intentional vulns configured (AS-REP roasting, weak SPN)
- [ ] BloodHound tested against lab AD

### Web App Lab
- [ ] DVWA — all difficulty levels tested
- [ ] Additional: Vulnhub box downloaded (specify: )

---

## Session Log

### Template entry (copy for each session)
```
## YYYY-MM-DD
**Goal:**
**Accomplished:**
**Issues encountered:**
**Next steps:**
```

---

## Known Issues / Fixes Needed

| Issue | VM | Priority | Status |
|-------|----|----------|--------|
| | | | |

---

## Networking Reference

```
Lab network: 192.168.56.0/24
Kali:        192.168.56.100
MS2:         192.168.56.101
DC (lab.local): 192.168.56.10
WS1:         192.168.56.20
WS2:         192.168.56.21
```

---

## Resources Used

| Resource | URL | Notes |
|----------|-----|-------|
| VirtualBox | https://www.virtualbox.org | |
| Kali Linux | https://www.kali.org/get-kali/ | |
| Metasploitable 2 | https://sourceforge.net/projects/metasploitable/ | |
| MS Eval Center | https://www.microsoft.com/en-us/evalcenter/ | |
| VulnHub | https://www.vulnhub.com | |

---

## Related Notes
- Home-Lab-Setup — full setup guide and build instructions
- Learning-Log-Index — session learning logs
- Project-Template — general project template
- Penetration-Testing-Methodology — methodology reference for lab practice

---
<- [[Projects-MOC]]