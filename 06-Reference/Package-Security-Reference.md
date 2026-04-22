---
title: Package Security Reference
tags:
  - reference
  - security
  - npm
  - pip
  - supply-chain
topic: Reference
difficulty: foundational
created: 2026-04-14
updated: 2026-04-14
source: OWASP Supply Chain Security, npm docs, pip docs
---

# Package Security Reference

---

## The Simple 3-Step Process — Always Follow This Order

> [!important] PRE = before you install. POST = after you install. Never skip PRE.

| Step | When | What You Do | Command |
|------|------|-------------|---------|
| **1. PRE — Verify** | Before installing | Check it's legit on npmjs.com or pypi.org | Manual check |
| **2. PRE — Inspect** | Before installing | See what it pulls in | `npm info [package] dependencies` |
| **3. POST — Audit** | After installing | Scan for known vulnerabilities | `npm audit` or `pip-audit` |

**Why this order matters:**
- PRE checks = YOUR eyes catching obvious fakes and typos
- POST audit = DATABASE catching known vulnerabilities you can't see yourself
- Both are needed — neither replaces the other

---

## Before Installing ANY Package

> [!important] Always verify before you install — takes 60 seconds, could save your machine

1. **Who published it?** — Official company package or random community package?
2. **Exact name match** — Attackers use typos: `@anthropic-ai/claude-codes` vs `@anthropic-ai/claude-code`
3. **Check the registry directly:**
   - npm → `npmjs.com/package/[package-name]`
   - pip → `pypi.org/project/[package-name]`
4. **Download count** — Millions of downloads = legitimate. Dozens = red flag
5. **Last updated** — Abandoned packages are risky
6. **Google it first** — Search `"package-name" official` and verify the company references it

---

## Supply Chain Attacks — What They Are

> [!warning] This is how real attackers get onto developer machines

A supply chain attack is when an attacker:
1. Publishes a **fake package** with a name similar to a real one (typosquatting)
2. Waits for someone to mistype the install command
3. Their malicious code runs automatically on install

**Real examples:**
- `event-stream` (npm) — malicious code injected into legitimate package
- `ctx` and `rc` (pip) — typosquatted packages stealing environment variables
- `colourama` (pip) — fake version of `colorama` stealing crypto wallet data

**The defense:** Slow down, verify the exact name, check the source.

---

## Auditing npm Installs

### See everything installed globally
```powershell
npm list -g --depth=0
```

### Scan for known vulnerabilities
```powershell
npm audit
```

### Fix vulnerabilities automatically (where possible)
```powershell
npm audit fix
```

### Check a specific package before installing
```powershell
npm info [package-name]
```
Shows publisher, download stats, last publish date — verify before installing.

### Remove a suspicious package
```powershell
npm uninstall -g [package-name]
```

---

## Auditing pip Installs

### See everything installed
```powershell
pip list
```

### Scan for known vulnerabilities (requires pip-audit)
```powershell
pip install pip-audit
pip-audit
```

### Check a specific package before installing
```powershell
pip index versions [package-name]
```

### Remove a suspicious package
```powershell
pip uninstall [package-name]
```

---

## Red Flags — When to Stop and Research

| Red Flag | Why It Matters |
|----------|---------------|
| Package name is very similar to a popular one | Typosquatting |
| Published very recently with no history | New malicious package |
| Very low download count | Not widely used = not vetted |
| Install requires admin/root unexpectedly | Unusual permission grab |
| Package not referenced on official company site | May not be legitimate |
| Lots of dependencies for a simple tool | Hidden malicious deps |

---

## Safe Install Habits

```powershell
# Always check npm before installing
npm info @anthropic-ai/claude-code

# Always use exact package names — no guessing
npm install -g @anthropic-ai/claude-code

# After installing — audit immediately
npm audit

# Periodically check all global installs
npm list -g --depth=0
```

---

## Related Notes
- Container-Docker-Security — supply chain risks in Docker images
- Vulnerability-Management — broader vulnerability tracking
- PowerShell-Security — secure scripting practices

---
<- [[Reference-MOC]]