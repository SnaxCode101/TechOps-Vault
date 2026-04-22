---
title: Learning Log — 2026-03-23 (Session 2)
tags: |-
  - learning-log
  - daily
date: 2026-03-23
week: 13
---

# 📓 Learning Log — 2026-03-23 (Session 2)

## ✅ What I Studied Today
- TCP/IP model, headers, flags, and 3-way handshake
- ICMP types and security relevance
- Cryptography fundamentals: symmetric, asymmetric, hashing, PKI, TLS
- Gobuster: dir, dns, and vhost modes; wordlist strategy
- Metasploit: module types, Meterpreter, msfvenom payload generation
- Reference cheat sheets: Common Ports and Linux Commands

## 🧠 Key Takeaways
1. TCP flags are fingerprints — SYN/RST patterns tell you if a port is open, closed, or filtered without ever completing a handshake.
2. Never use MD5 or SHA-1 for password hashing — use bcrypt or Argon2 with salting.
3. Meterpreter runs in memory and avoids touching disk — this is why it's preferred over a simple shell in real engagements.

## ❓ Questions / Gaps
- How does Kerberoasting work at the protocol level?
- What makes Argon2 resistant to GPU-based cracking vs bcrypt?
- How does msfvenom encoding actually evade AV signatures?

## 🔗 Notes Created Today
- TCP-IP-Fundamentals
- Cryptography-Basics
- Gobuster
- Metasploit
- Common-Ports
- Linux-Commands

## ⏱️ Time Spent
| Activity | Time |
|---|---|
| Vault build (session 2) | — |
| **Total** | — |

## 🎯 Next Session Focus
- Add OWASP-Top-10 to Fundamentals
- Add John-the-Ripper or Hashcat tool note
- Start first lab write-up (TryHackMe: Basic Pentesting or similar)
- Create cert study folder structure under 05-Certs/

---
<- [[Learning-Log-MOC]]