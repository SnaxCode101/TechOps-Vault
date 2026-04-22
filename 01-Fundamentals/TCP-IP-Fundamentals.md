---
title: TCP/IP Fundamentals
tags:
  - fundamentals
  - networking
  - protocols
  - tcp
  - ip
  - foundational
topic: Networking
difficulty: foundational
created: 2026-03-23
updated: 2026-04-04
source: RFC 791 (IPv4), RFC 793 (TCP), RFC 768 (UDP), RFC 792 (ICMP)
---

# TCP/IP Fundamentals

## Overview
TCP/IP is the foundational protocol suite of the internet and modern networks. It defines how data is addressed, routed, transmitted, and received across interconnected networks. Understanding TCP/IP at a packet level is essential for network-based attack and defense, traffic analysis, and penetration testing.

## Why It Matters in Security
Nearly every network-based attack — port scanning, session hijacking, DoS, MITM — operates at the TCP/IP layer. Tools like Nmap, Wireshark, and Metasploit all operate on TCP/IP mechanics. You cannot interpret packet captures or craft exploits without understanding how these protocols behave.

---

## Core Details

### The TCP/IP Model (4 Layers)

| Layer | Name | OSI Equivalent | Protocols |
|---|---|---|---|
| 4 | Application | 5, 6, 7 | HTTP, DNS, SSH, FTP, SMTP |
| 3 | Transport | 4 | TCP, UDP |
| 2 | Internet | 3 | IP, ICMP, ARP |
| 1 | Network Access | 1, 2 | Ethernet, Wi-Fi |

---

### IP (Internet Protocol) — Layer 3

IP is a **connectionless, best-effort** protocol responsible for logical addressing and routing packets across networks.

**IPv4 Header — Key Fields:**
| Field | Size | Purpose |
|---|---|---|
| Version | 4 bits | IP version (4 or 6) |
| TTL | 8 bits | Decremented at each hop; packet dropped at 0 |
| Protocol | 8 bits | Upper layer: 6=TCP, 17=UDP, 1=ICMP |
| Source IP | 32 bits | Sender's IP address |
| Destination IP | 32 bits | Receiver's IP address |

**Security relevance of TTL:**
- Traceroute exploits TTL decrement to map network hops
- OS fingerprinting: Windows defaults TTL=128, Linux=64, Cisco=255
- Unusually low TTL can indicate the packet has traversed many hops (potential relay/proxy)

---

### TCP (Transmission Control Protocol) — Layer 4

TCP is **connection-oriented** and provides reliable, ordered, error-checked delivery of a byte stream.

#### TCP Header — Key Fields
| Field | Size | Purpose |
|---|---|---|
| Source Port | 16 bits | Sending port |
| Destination Port | 16 bits | Receiving port |
| Sequence Number | 32 bits | Tracks byte ordering |
| Acknowledgment Number | 32 bits | Next expected byte from peer |
| Flags | 9 bits | Control bits (SYN, ACK, FIN, RST, PSH, URG) |
| Window Size | 16 bits | Flow control — how much data receiver can accept |

#### TCP Flags
| Flag | Hex | Meaning |
|---|---|---|
| SYN | 0x02 | Synchronize — initiate connection |
| ACK | 0x10 | Acknowledgment |
| FIN | 0x01 | Finish — graceful close |
| RST | 0x04 | Reset — abrupt close |
| PSH | 0x08 | Push data immediately to application |
| URG | 0x20 | Urgent data |

#### Three-Way Handshake (Connection Establishment)
```
Client                        Server
  |--- SYN (seq=x) ---------->|
  |<-- SYN-ACK (seq=y,ack=x+1)|
  |--- ACK (ack=y+1) -------->|
  [Connection Established]
```

#### Four-Way Handshake (Connection Termination)
```
Client                        Server
  |--- FIN ------------------>|
  |<-- ACK -------------------|
  |<-- FIN -------------------|
  |--- ACK ------------------>|
  [Connection Closed]
```

#### RST (Reset)
Sent when a connection is refused or abruptly terminated. In Nmap SYN scans:
- **SYN → SYN-ACK** = port **open**
- **SYN → RST** = port **closed**
- **SYN → no response** = port **filtered**

---

### UDP (User Datagram Protocol) — Layer 4

UDP is **connectionless** — no handshake, no guaranteed delivery, no ordering. Faster but unreliable.

**Uses:** DNS (53), DHCP (67/68), VoIP, streaming, SNMP (161), NTP (123).

**Security relevance:**
- UDP is harder to scan (no SYN/RST response for closed ports → `open|filtered`)
- DNS amplification DDoS exploits UDP's lack of verification
- Many UDP services have historically weak authentication

---

### ICMP (Internet Control Message Protocol) — Layer 3

ICMP carries control and error messages for IP. It has no port numbers.

**Common ICMP Types:**
| Type | Code | Description |
|---|---|---|
| 0 | 0 | Echo Reply (ping response) |
| 3 | 0-15 | Destination Unreachable (various reasons) |
| 3 | 3 | Port Unreachable |
| 8 | 0 | Echo Request (ping) |
| 11 | 0 | Time Exceeded (TTL=0, used by traceroute) |

**Security relevance:**
- ICMP tunneling can be used for covert C2 channels (data hidden in echo payloads)
- Blocking ICMP entirely breaks traceroute and legitimate troubleshooting
- ICMP redirect messages can be abused for MITM routing attacks
- Ping sweeps use ICMP type 8/0 for host discovery

---

## Common Attack Vectors

| Attack | Protocol | How It Works |
|---|---|---|
| SYN Flood | TCP | Send massive SYN packets without completing handshake; exhausts server connection table |
| Session Hijacking | TCP | Predict or steal sequence numbers to inject into an established TCP stream |
| IP Spoofing | IP | Forge source IP address in packet header |
| ICMP Tunneling | ICMP | Encode data in ICMP echo payloads to bypass firewalls |
| UDP Amplification | UDP | Forge victim's IP as source; server sends large replies to victim |
| TTL Manipulation | IP | Craft packets with low TTL to exploit IDS evasion or map hops |

---

## Port Ranges

| Range | Name | Description |
|---|---|---|
| 0 – 1023 | Well-Known Ports | Assigned to standard services (HTTP=80, SSH=22) |
| 1024 – 49151 | Registered Ports | Vendor/app registered (MySQL=3306, RDP=3389) |
| 49152 – 65535 | Dynamic/Ephemeral | Assigned by OS for outgoing connections |

---

## Key Terms
| Term | Definition |
|---|---|
| Socket | A combination of IP address + port + protocol identifying a unique connection endpoint |
| Segment | TCP's name for a PDU at the Transport layer |
| Datagram | UDP's name for a PDU at the Transport layer |
| Packet | IP's name for a PDU at the Network layer |
| Frame | Data Link layer PDU (wraps packets with MAC addresses) |
| Ephemeral Port | Temporary port assigned by OS for the client side of a connection |

## Related Notes
- OSI-Model
- Networking-Basics
- Nmap
- Wireshark

## Sources

Internet Engineering Task Force. (1981). *Internet protocol* (RFC 791). IETF. https://www.rfc-editor.org/rfc/rfc791

Internet Engineering Task Force. (1981). *Transmission control protocol* (RFC 793). IETF. https://www.rfc-editor.org/rfc/rfc793

Internet Engineering Task Force. (1980). *User datagram protocol* (RFC 768). IETF. https://www.rfc-editor.org/rfc/rfc768

Internet Engineering Task Force. (1981). *Internet control message protocol* (RFC 792). IETF. https://www.rfc-editor.org/rfc/rfc792

## See Also

- Networking-Basics
- OSI-Model
- Wireshark
- Nmap
- Common-Ports

---
<- [[Fundamentals-MOC]]