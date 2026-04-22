---
title: IoT/OT Security Fundamentals
type: reference
category: medical-devices
created: 2026-03-27
updated: 2026-03-27
tags:
  - iot
  - ot
  - industrial
  - scada
  - purdue-model
  - protocols
  - attack-surface
  - medical-devices
  - healthcare
  - intermediate
source: NIST SP 800-183, IEC 62443, MITRE ATT&CK for ICS, OWASP IoT Top 10
difficulty: intermediate
---

# 📡 IoT/OT Security Fundamentals

## Overview

**IoT (Internet of Things)** and **OT (Operational Technology)** encompass network-connected physical devices and industrial control systems. In healthcare, this includes medical devices, building management systems (HVAC, elevators), and facility monitoring. IoT/OT security differs fundamentally from traditional IT security because these systems prioritise **availability and safety** over confidentiality.

---

## IT vs OT vs IoT — Key Differences

| Aspect | IT | OT | IoT |
|---|---|---|---|
| Priority | Confidentiality | Availability / Safety | Varies (often availability) |
| Lifecycle | 3–5 years | 10–25 years | 3–10 years |
| Patching | Regular patch cycles | Rare; vendor-coordinated | Often impossible |
| OS | Windows, Linux, macOS | RTOS, embedded Linux, proprietary | Embedded firmware, RTOS |
| Protocols | TCP/IP, HTTP, SSH | Modbus, BACnet, OPC-UA, DICOM | MQTT, CoAP, Zigbee, BLE |
| Scanning tolerance | Normal | Can cause outages/safety events | Limited |
| CIA triad emphasis | C-I-A | A-I-C (availability first) | A-I-C |

---

## Purdue Model (Reference Architecture)

The **Purdue Enterprise Reference Architecture** defines network zones for industrial/OT environments. Healthcare adapts this model:

```
Level 5 — Enterprise Network (corporate IT, internet)
Level 4 — Business Planning (ERP, email, business apps)
    ────── IT/OT DMZ (firewalls, data diodes, jump servers) ──────
Level 3 — Site Operations (clinical apps, EHR, PACS servers)
Level 2 — Supervisory (SCADA, HMI, device management servers)
Level 1 — Control (PLCs, RTUs, device controllers, pump servers)
Level 0 — Physical Process (sensors, actuators, medical devices)
```

**Healthcare adaptation:**
| Purdue Level | Healthcare Equivalent |
|---|---|
| Level 5 | Corporate IT, internet-facing services |
| Level 4 | Administrative systems (HR, finance, email) |
| DMZ | HL7/FHIR integration engines, vendor VPN gateways |
| Level 3 | EHR servers, PACS, clinical workstations |
| Level 2 | Device management servers, pump drug library servers |
| Level 1 | Medical device controllers, infusion pump servers |
| Level 0 | Bedside monitors, infusion pumps, ventilators, sensors |

**Key rule:** Traffic flows vertically through adjacent levels — never bypass levels (e.g., Level 0 device should never communicate directly with Level 5 internet).

---

## Common IoT/OT Protocols

### Healthcare-Relevant Protocols

| Protocol | Use | Port | Security Notes |
|---|---|---|---|
| DICOM | Medical imaging transfer | 104, 11112 | Often unencrypted; supports TLS but rarely enabled |
| HL7v2 | Clinical messaging (ADT, orders, results) | 2575 | Cleartext by default; contains PHI |
| FHIR (REST) | Modern clinical API | 443 | HTTPS/TLS; more secure than HL7v2 |
| BACnet | Building automation (HVAC) | 47808 | No built-in authentication; vulnerable to spoofing |
| Modbus | Industrial control | 502 | No authentication or encryption; highly vulnerable |
| MQTT | Lightweight IoT messaging | 1883/8883 | 8883 = TLS; 1883 = cleartext; common in IoT/wearables |
| CoAP | Constrained application protocol (IoT) | 5683/5684 | UDP-based; DTLS for security; used in resource-constrained devices |
| Zigbee | Wireless sensor networks | 2.4 GHz | AES-128 encryption available but key management weak |
| BLE | Bluetooth Low Energy | 2.4 GHz | Pairing vulnerabilities; used in wearables, proximity tracking |
| OPC-UA | Industrial data exchange | 4840 | Built-in security (TLS, certs); modern replacement for OPC Classic |

---

## IoT/OT Attack Surface

### Common Attack Vectors

**1. Default and hardcoded credentials**
```
Many IoT/OT devices ship with:
- admin/admin, admin/1234, root/root
- Hardcoded service accounts that cannot be changed
- Shared credentials across device fleet
Attack: Shodan/Censys scan → find exposed device → log in with default creds
```

**2. Unencrypted protocols**
```
HL7v2, Modbus, BACnet, DICOM (default) transmit in cleartext
Attack: network sniffing → capture PHI, device commands, or credentials
Mitigation: TLS wrapping, VPN tunnels, or protocol-aware firewalls
```

**3. Lack of patching / firmware updates**
```
Device vendor provides no patches, or patches require clinical downtime
OS: Windows XP, Windows 7, embedded Linux (kernel 2.x)
Attack: known CVE exploitation against unpatched OS/libraries
Mitigation: network segmentation, virtual patching (IPS rules)
```

**4. Insecure update mechanisms**
```
Firmware updates over HTTP (not HTTPS)
No code signing — attacker can push malicious firmware
Attack: MITM during firmware update → replace with backdoored firmware
Mitigation: require signed updates, verify checksums, update over encrypted channel
```

**5. Exposed management interfaces**
```
Telnet, HTTP admin panels, debug ports left enabled
Attack: unauthenticated access to device configuration
Mitigation: disable unnecessary services, restrict management access to management VLAN
```

**6. Physical access**
```
USB ports, serial consoles, JTAG debug headers on devices
Attack: physical access → firmware extraction, credential dumping, malware injection
Mitigation: disable USB ports, physical security, tamper-evident seals
```

---

## OWASP IoT Top 10 (2018)

| # | Vulnerability |
|---|---|
| I1 | Weak, guessable, or hardcoded passwords |
| I2 | Insecure network services |
| I3 | Insecure ecosystem interfaces (APIs, cloud, mobile) |
| I4 | Lack of secure update mechanism |
| I5 | Use of insecure or outdated components |
| I6 | Insufficient privacy protection |
| I7 | Insecure data transfer and storage |
| I8 | Lack of device management |
| I9 | Insecure default settings |
| I10 | Lack of physical hardening |

> Source: https://owasp.org/www-project-internet-of-things/

---

## MITRE ATT&CK for ICS

MITRE maintains a dedicated ATT&CK matrix for Industrial Control Systems (ICS), applicable to medical device environments.

**Key ICS techniques relevant to healthcare:**

| Technique | Description | Healthcare Example |
|---|---|---|
| T0886 — Remote Services | Exploit remote access to OT systems | Vendor VPN to medical device |
| T0859 — Valid Accounts | Use legitimate credentials | Stolen biomed engineer credentials |
| T0855 — Unauthorized Command Message | Send malicious commands to device | Spoofed Modbus/BACnet commands |
| T0826 — Loss of Availability | Disrupt device operation | Ransomware disabling clinical systems |
| T0882 — Theft of Operational Info | Steal process data | Exfiltrate patient telemetry data |
| T0831 — Manipulation of Control | Alter device behaviour | Change infusion pump dosage parameters |

> Source: https://attack.mitre.org/techniques/ics/

---

## Security Controls for IoT/OT Environments

### Network Controls (Primary Defence Layer)

```
□ Segmentation: dedicated VLANs per device type (see Medical-Device-Network-Security)
□ Firewalls: zone-based with allow-list rules between OT and IT zones
□ NAC: profile-based VLAN assignment; quarantine unknown devices
□ Passive monitoring: SPAN/TAP-based IDS for OT protocols
□ DNS sinkholing: redirect malicious domains from clinical VLANs
```

### Device Controls (Where Possible)

```
□ Change default credentials (if the device allows it)
□ Disable unnecessary services (Telnet, FTP, HTTP admin)
□ Enable TLS/encryption where supported
□ Enable audit logging; forward to SIEM
□ Apply firmware updates when available (coordinate with vendor and clinical schedule)
```

### Compensating Controls (For Unpatchable Devices)

```
□ Virtual patching: IPS rules that block known exploit traffic targeting the device's CVEs
□ Network ACLs: restrict communication to only required peers
□ Increased monitoring: dedicated IDS rules for the device
□ Scheduled replacement: document end-of-life date and plan for replacement
□ Risk acceptance: formal risk acceptance signed by appropriate authority (CISO, clinical leadership)
```

---

## IoT/OT Security Tools

| Tool | Purpose |
|---|---|
| Claroty / Medigate | Medical device visibility, risk assessment, network monitoring |
| Forescout | NAC + IoT device profiling |
| Nozomi Networks | OT/IoT network monitoring and threat detection |
| Armis | Agentless device security platform |
| Shodan / Censys | Internet-facing device discovery (external reconnaissance) |
| Wireshark | Protocol analysis (DICOM, HL7, Modbus, BACnet) |
| Snort / Suricata | IDS/IPS with OT protocol rules |

---

## Related Notes

- Medical-Devices-Overview — Section overview
- Medical-Device-Network-Security — Network architecture for clinical devices
- Network-Defense-Blue-Team — IDS/IPS, SIEM operations
- Networking-Basics — OSI model, TCP/IP fundamentals
- Common-Ports — Port reference
- Wireshark — Packet capture and protocol analysis

---

## References

- NIST SP 800-183: Networks of Things: https://csrc.nist.gov/publications/detail/sp/800-183/final
- IEC 62443: Industrial communication networks — Network and system security
- MITRE ATT&CK for ICS: https://attack.mitre.org/techniques/ics/
- OWASP IoT Top 10: https://owasp.org/www-project-internet-of-things/
- CISA ICS Security: https://www.cisa.gov/topics/industrial-control-systems

---
*Note created: 2026-03-27*

---
<- [[Medical-Devices-MOC]]