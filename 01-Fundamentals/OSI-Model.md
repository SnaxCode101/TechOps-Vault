---
title: OSI Model
tags:
  - fundamentals
  - networking
  - protocols
  - foundational
topic: Networking
difficulty: foundational
created: 2026-03-23
updated: 2026-04-04
source: ISO/IEC 7498-1, CompTIA Network+
---

# OSI Model

## Overview
The Open Systems Interconnection (OSI) model is a conceptual framework developed by ISO that standardizes network communication into **seven distinct layers**. It is used to troubleshoot network issues, understand protocol behavior, and reason about where in the stack attacks and defenses operate.

## Why It Matters in Security
Attackers operate at specific OSI layers. Knowing which layer a tool or attack targets helps you understand scope of impact and appropriate countermeasures. Firewalls, IDS/IPS, and protocol analysis tools are all framed around OSI layers.

## Core Details

| Layer | Number | Name | Function | Example Protocols/Devices |
|---|---|---|---|---|
| 7 | Application | Application | User-facing services and APIs | HTTP, HTTPS, DNS, FTP, SMTP, SSH |
| 6 | Presentation | Presentation | Data encoding, encryption, compression | SSL/TLS, JPEG, ASCII |
| 5 | Session | Session | Establishing, managing, terminating sessions | NetBIOS, RPC, PPTP |
| 4 | Transport | Transport | End-to-end delivery, segmentation, flow control | TCP, UDP |
| 3 | Network | Network | Logical addressing and routing | IP, ICMP, IPSec |
| 2 | Data Link | Data Link | Physical addressing (MAC), framing, error detection | Ethernet, ARP, VLANs, Switches |
| 1 | Physical | Physical | Raw bit transmission over physical media | Cables, hubs, fiber, radio |

### Memory Aid (top to bottom): **All People Seem To Need Data Processing**

---

## Security Relevance by Layer

| Layer | Common Attacks | Security Controls |
|---|---|---|
| 7 — Application | SQL injection, XSS, CSRF, phishing | WAF, input validation, auth controls |
| 6 — Presentation | SSL stripping, certificate spoofing | Enforce TLS, cert pinning |
| 5 — Session | Session hijacking, replay attacks | Session tokens, timeouts, MFA |
| 4 — Transport | SYN flood, port scanning | Firewalls, rate limiting, TCP hardening |
| 3 — Network | IP spoofing, ICMP tunneling, routing attacks | ACLs, IPSec, ingress/egress filtering |
| 2 — Data Link | ARP spoofing, MAC flooding, VLAN hopping | Dynamic ARP inspection, port security |
| 1 — Physical | Cable tapping, hardware implants | Physical access controls, tamper detection |

---

## TCP/IP vs OSI
The TCP/IP model (4 layers) maps to OSI but is what's actually implemented in modern networks:

| TCP/IP Layer | OSI Equivalent Layers |
|---|---|
| Application | 5, 6, 7 |
| Transport | 4 |
| Internet | 3 |
| Network Access | 1, 2 |

## Key Terms
| Term | Definition |
|---|---|
| Encapsulation | The process of wrapping data with protocol headers as it moves down the OSI stack |
| PDU | Protocol Data Unit — the data format at each layer (bit, frame, packet, segment, data) |
| ARP | Address Resolution Protocol — maps IP addresses to MAC addresses at Layer 2 |
| MAC Address | Media Access Control address — a hardware identifier assigned to a network interface |

## Related Notes
- CIA-Triad
- Networking-Basics
- TCP-IP-Fundamentals
- Wireshark
- Nmap

## Sources

International Organization for Standardization. (1994). *Information technology — open systems interconnection — basic reference model: The basic model* (ISO/IEC 7498-1). ISO/IEC.

CompTIA. (2024). *CompTIA network+ study guide*. CompTIA.

## See Also

- Networking-Basics
- TCP-IP-Fundamentals
- Wireshark
- Nmap

---
<- [[Fundamentals-MOC]]