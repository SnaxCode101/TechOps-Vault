---
title: "Lab Write-Up Template"
type: template
category: lab
date: 2026-03-24
tags: [template, lab]
---

# {{Title}}

## Lab Info

| Field | Value |
|---|---|
| Platform | {{THM / HTB / Custom}} |
| Room / Machine | {{Name}} |
| Difficulty | {{Easy / Medium / Hard}} |
| OS | {{Linux / Windows}} |
| IP | {{10.10.x.x or tun0}} |
| Date | {{YYYY-MM-DD}} |

---

## Objectives

- [ ] Objective 1
- [ ] Objective 2
- [ ] Objective 3

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -oN nmap/initial {{IP}}
```

**Open ports:**

| Port | Service | Version |
|---|---|---|
| | | |

---

## Foothold

**Vulnerability:** {{CVE or description}}

```bash
# Exploit commands
```

**Result:** {{Shell type and user}}

---

## Privilege Escalation

```bash
# PrivEsc enumeration (sudo -l, linpeas, winpeas)
```

**Vector:** {{Description}}

**Result:** {{Root/SYSTEM obtained}}

---

## Flags

| Flag | Location |
|---|---|
| User | `/home/{{user}}/user.txt` |
| Root | `/root/root.txt` |

---

## Key Takeaways

- 
- 

## Tools Used

- [[]]
- [[]]

## References

- 

---
*Write-up completed: {{YYYY-MM-DD}}*
