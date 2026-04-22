---
title: Medical Device Network Security
type: reference
category: medical-devices
created: 2026-03-27
updated: 2026-03-27
tags:
  - medical-devices
  - network-security
  - segmentation
  - vlan
  - nac
  - ids
  - clinical-network
  - iot
  - intermediate
source: NIST SP 1800-8, NIST SP 800-183, IEC 80001-1, CISA medical device guidance
difficulty: intermediate
---

# 🌐 Medical Device Network Security

## Overview

Medical devices on hospital networks require specialised network architecture. Unlike traditional IT endpoints, clinical devices often cannot run endpoint agents, accept patches in real time, or tolerate network disruptions. Network-level controls — segmentation, monitoring, and access control — are the primary defence layer.

---

## Clinical Network Architecture — Reference Model

```
┌─────────────────────────────────────────────┐
│              Enterprise Zone                 │
│  (Corporate IT: email, ERP, HR, finance)     │
│  Standard IT security controls apply         │
├─────────────────────────────────────────────┤
│              DMZ / Integration Zone          │
│  (Interface engines, HL7/FHIR gateways,     │
│   PACS brokers, vendor remote access)        │
├─────────────────────────────────────────────┤
│              Clinical Zone                   │
│  (EHR workstations, clinical apps,           │
│   nurse call systems)                        │
├─────────────────────────────────────────────┤
│              Medical Device Zone             │
│  (Infusion pumps, patient monitors,          │
│   ventilators, imaging systems)              │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐       │
│  │ VLAN 10 │ │ VLAN 20 │ │ VLAN 30 │       │
│  │ Imaging │ │ Biomed  │ │ IoT/    │       │
│  │ Systems │ │ Devices │ │ Wearable│       │
│  └─────────┘ └─────────┘ └─────────┘       │
├─────────────────────────────────────────────┤
│              Building Management (BMS)       │
│  (HVAC, elevators, physical security — OT)   │
└─────────────────────────────────────────────┘
```

**Key principle:** Medical devices should NEVER sit on the same flat network as enterprise IT. Zone-based architecture with firewalls between zones enforces least-privilege communication.

---

## Network Segmentation Strategy

### VLAN Design for Clinical Devices

```
VLAN strategy (example):
VLAN 100 — Corporate IT (workstations, printers)
VLAN 200 — Clinical applications (EHR workstations, clinical PCs)
VLAN 300 — Medical imaging (PACS, CT, MRI, X-ray)
VLAN 310 — Infusion systems (pumps, drug libraries)
VLAN 320 — Patient monitoring (bedside monitors, telemetry)
VLAN 330 — Biomedical devices (ventilators, anaesthesia machines)
VLAN 340 — IoT / wearables (nurse call, asset tracking, RTLS)
VLAN 400 — Guest/patient Wi-Fi (isolated)
VLAN 500 — Building management (HVAC, elevators)
VLAN 900 — Management / out-of-band (switch management, iDRAC)
```

### Firewall Rules Between Zones

**Medical Device Zone → Clinical Zone:**
```
ALLOW: specific ports needed for clinical workflow
  e.g., HL7 (port 2575), DICOM (port 104/11112), HTTPS (443)
DENY: all other traffic by default

Example ACL:
permit tcp VLAN300 -> VLAN200 port 104   (DICOM to PACS broker)
permit tcp VLAN310 -> VLAN200 port 443   (pump drug library update from EHR)
deny   ip  VLAN300 -> VLAN100            (no imaging → corporate)
deny   ip  VLAN310 -> VLAN100            (no pumps → corporate)
```

**Medical Device Zone → Internet:**
```
DENY by default
ALLOW: only via proxy for specific vendor update servers (whitelisted URLs)
Vendor remote access: VPN with MFA through a jump server in the DMZ
```

### Micro-Segmentation

For high-risk devices, apply per-device or per-device-type firewall rules:

```
Example: Infusion pump micro-segment
- Pump communicates ONLY with:
  → Drug library server (VLAN 200, specific IP, port 443)
  → PACS/EHR integration broker (VLAN 200, specific IP, port 2575)
  → NTP server (VLAN 900, port 123)
  → DNS server (VLAN 900, port 53)
- ALL other traffic: DENY + LOG
```

---

## Network Access Control (NAC)

NAC ensures only authorised, profiled devices connect to clinical VLANs.

**NAC workflow for medical devices:**
```
Device connects to switch port
    ↓ 802.1X or MAC authentication
NAC profiles the device (MAC OUI, DHCP fingerprint, network behaviour)
    ↓
Device matched to profile → assigned to correct VLAN
    ↓
Unrecognised device → quarantine VLAN (no network access)
```

**NAC considerations for medical devices:**
- Many medical devices do NOT support 802.1X — use MAC Authentication Bypass (MAB) with profiling
- Maintain a device inventory/whitelist with MAC addresses
- Profile-based VLAN assignment: NAC identifies "GE Patient Monitor" → assigns to VLAN 320
- Common NAC platforms in healthcare: Cisco ISE, Forescout, Aruba ClearPass, Medigate/Claroty

**Forescout / Medigate / Claroty (medical device security platforms):**
These platforms passively profile medical devices by analysing network traffic:
- Identify device type, manufacturer, model, OS, firmware version
- Classify risk level per device
- Detect anomalous behaviour (unexpected connections, protocol deviations)
- Integrate with NAC to enforce segmentation policies
- Provide device inventory and SBOM correlation

---

## Passive Network Monitoring and IDS

Medical devices typically cannot run endpoint agents. **Passive network monitoring** is the primary detection mechanism.

### SPAN/Mirror Port Architecture

```
Clinical switch
    ↓ SPAN port (copies all traffic)
Network sensor (IDS / medical device security platform)
    ↓ analyse traffic passively
Alerts → SIEM / SOC
```

**Why passive:** Active scanning (Nmap, Nessus) can crash or disrupt medical devices. Always use passive monitoring unless the vendor explicitly confirms the device tolerates active scanning.

### What to Monitor

| Indicator | Why It Matters |
|---|---|
| Unexpected outbound connections | Device may be compromised or misconfigured |
| Connections to non-whitelisted IPs | Potential C2 communication |
| Protocol anomalies (e.g., DICOM to non-PACS server) | Lateral movement or data exfiltration |
| New device on clinical VLAN | Rogue device detection |
| Firmware version changes | Unauthorised firmware modification |
| Cleartext credential transmission | HL7v2 and some device protocols transmit in cleartext |
| Unusual traffic volume | Potential DDoS or data exfiltration |

### SIEM Integration

Forward medical device alerts to the hospital SIEM:
- Correlate with enterprise IT alerts for cross-zone attack detection
- Create dashboards for: medical device alert volume, top talkers, new devices, unresolved vulnerabilities
- Integrate CISA ICS-CERT advisories as threat intelligence feeds

---

## Wireless Security for Clinical Devices

Many medical devices connect via Wi-Fi (telemetry, wearables, mobile workstations).

**Wireless best practices:**
```
□ Dedicated clinical SSID with WPA3-Enterprise (or WPA2-Enterprise minimum)
□ 802.1X authentication where device supports it
□ PSK (Pre-Shared Key) for devices that cannot do 802.1X — rotate keys on schedule
□ Separate SSID for guest/patient Wi-Fi (fully isolated)
□ Wireless IDS/IPS to detect rogue APs and deauth attacks
□ RF shielding in sensitive areas (OR suites, ICU) if interference is a concern
□ QoS prioritisation for clinical traffic over guest/administrative traffic
```

---

## Vendor Remote Access

Device vendors frequently require remote access for maintenance, troubleshooting, and updates.

**Secure vendor remote access architecture:**
```
Vendor → MFA + VPN → Jump server (DMZ)
    ↓ RDP/SSH (session recorded)
Target device (Medical Device Zone)

Requirements:
□ MFA enforced on all vendor VPN connections
□ Time-limited access windows (vendor access enabled only when needed)
□ Session recording for all vendor remote sessions
□ Vendor access restricted to specific devices (not broad network access)
□ All vendor sessions logged and reviewed
□ Separate vendor accounts per technician (no shared credentials)
```

**Never allow:** direct internet-facing RDP/SSH to medical devices, vendor-controlled VPN appliances without hospital IT oversight, or persistent always-on vendor connections.

---

## DNS and NTP for Medical Devices

Medical devices need DNS and NTP for basic operation. Configure these securely:

```
DNS:
□ Point devices to internal DNS servers (not public 8.8.8.8)
□ Internal DNS enables logging of all device DNS queries
□ Block DNS-over-HTTPS (DoH) from clinical VLANs — prevents evasion of DNS monitoring

NTP:
□ Internal NTP servers synchronised to trusted upstream sources
□ Accurate time is critical for: clinical data timestamps, audit logs, certificate validation
□ NTP traffic should be restricted to internal NTP servers only
```

---

## Common Scenarios

**Scenario 1:** A new CT scanner is being deployed. The vendor says it needs "full internet access." How do you respond?
→ Request the **MDS2 form** for specific ports and protocols. Configure a **proxy whitelist** for only the required vendor update URLs. Place the scanner on the **imaging VLAN** with firewall rules allowing only necessary clinical and vendor traffic. Deny all other internet access.

**Scenario 2:** An infusion pump is communicating with an unknown IP address on the internet. What do you do?
→ **Isolate** the pump from the network immediately (clinical safety permitting — coordinate with nursing/clinical engineering). Investigate in SIEM/IDS logs. Check if the IP is a known vendor server or a potential C2. Follow the [[Medical-Device-Incident-Response]] procedure.

**Scenario 3:** Biomedical engineering wants to scan all medical devices with Nessus for vulnerability assessment. Is this safe?
→ **Not without vendor approval.** Active vulnerability scanning can crash or disrupt medical devices. Use **passive monitoring** platforms (Claroty, Medigate, Forescout) instead. If active scanning is required, coordinate with the vendor for a safe scanning profile and schedule during non-clinical hours.

---

## Related Notes

- Medical-Devices-Overview — Section overview
- IoT-OT-Security-Fundamentals — IoT/OT protocols and attack surfaces
- Network-Defense-Blue-Team — IDS/IPS, SIEM, SOC operations
- HIPAA-Technical-Safeguards — HIPAA technical controls
- Common-Ports — Port reference

---

## References

- NIST SP 1800-8: Securing Wireless Infusion Pumps in Healthcare Delivery Organizations: https://www.nccoe.nist.gov/projects/use-cases/medical-devices
- NIST SP 800-183: Networks of Things: https://csrc.nist.gov/publications/detail/sp/800-183/final
- IEC 80001-1: Risk management for IT networks incorporating medical devices
- CISA Medical Device Security: https://www.cisa.gov/medical-devices

---
*Note created: 2026-03-27*

---
<- [[Medical-Devices-MOC]]