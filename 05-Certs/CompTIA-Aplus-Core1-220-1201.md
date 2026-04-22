---
title: CompTIA A+ Core 1 (220-1201) Study Guide
tags:
  - certs
  - CompTIA
  - Aplus
  - hardware
  - networking
  - study-guide
  - foundational
topic: CompTIA A+ Core 1
difficulty: foundational
created: 2026-04-03
updated: 2026-04-03
source: CompTIA A+ Core 1 (220-1201) V15 Official Exam Objectives
---

# CompTIA A+ Core 1 (220-1201) Study Guide

## Overview
The CompTIA A+ Core 1 exam (220-1201) covers hardware, networking, mobile devices, virtualization, and cloud computing. Passing score: **675** on a scale of 100–900. Max 90 questions, 90 minutes.

**Exam weight by domain:**
| Domain | Weight |
|--------|--------|
| Mobile Devices | 13% |
| Networking | 23% |
| Hardware | 25% |
| Virtualization and Cloud Computing | 11% |
| Hardware and Network Troubleshooting | 28% |

---

## Domain 1 — Mobile Devices (13%)

### 1.1 Laptop Field-Replaceable Components
| Component | Notes |
|-----------|-------|
| Battery | Li-Ion or Li-Polymer; proprietary shape per model |
| RAM | SO-DIMM; some soldered (not replaceable) |
| Storage | M.2 NVMe or SATA SSD; some soldered |
| Keyboard | Ribbon-cable connected |
| Display | LCD (TN/IPS/VA) or OLED; older CCFL screens need inverter |
| Wi-Fi card | Mini PCIe or M.2; antenna wires route through lid |
| Camera | Ribbon cable to motherboard |
| DC jack | Common failure point; soldered or connector |
| Wi-Fi antenna | Routes through display lid; reconnect carefully after screen repair |

### Mobile Device Hardware
- **SoC:** ARM-based; CPU + GPU + modem + RAM on single die
- **Digitizer:** Touchscreen layer; separate from display panel
- **Accelerometer:** Orientation/motion detection
- **Gyroscope:** Rotation detection
- **GPS / A-GPS:** Standalone chip; A-GPS uses cell towers to assist
- **NFC:** 13.56 MHz; range ~4 cm; tap-to-pay, pairing
- **Biometrics:** Fingerprint (capacitive), Face ID (IR depth camera)

### 1.2 Accessories and Connection Methods
| Connection | Notes |
|------------|-------|
| USB-C | Data, video (DP Alt Mode), power (PD up to 240W) |
| Lightning | Apple proprietary; pre-2023 iPhone/iPad |
| Micro-USB | Legacy Android |
| Thunderbolt 3/4 | USB-C connector; 40 Gbps; daisy-chain |
| NFC | Tap-based; payment, pairing, data transfer |
| Bluetooth | Short-range; pair via PIN or NFC |
| Docking station | Connects via USB-C/TB; adds Ethernet, video, USB-A |
| Port replicator | Like docking station but without charging |
| Stylus | Active (digitizer) or passive |
| Trackpad/drawing pad | Input peripheral |

### 1.3 Network Connectivity and Application Support

#### Wi-Fi Standards (802.11)
| Standard              | Year | Frequency   | Max Speed | Notes                            |
| --------------------- | ---- | ----------- | --------- | -------------------------------- |
| 802.11                | 1997 | 2.4 GHz     | 2 Mbps    |                                  |
| 802.11b               | 1999 | 2.4 GHz     | 11 Mbps   | Long range; slow                 |
| 802.11a               | 1999 | 5 GHz       | 54 Mbps   | Less interference; shorter range |
| 802.11g               | 2003 | 2.4 GHz     | 54 Mbps   | Backward compatible with b       |
| 802.11n (Wi-Fi 4)     | 2009 | 2.4/5 GHz   | 600 Mbps  | MIMO; dual-band                  |
| 802.11ac (Wi-Fi 5)    | 2013 | 5 GHz       | 3.5 Gbps  | MU-MIMO; beamforming             |
| 802.11ax (Wi-Fi 6/6E) | 2019 | 2.4/5/6 GHz | 9.6 Gbps  | OFDMA; 6 GHz band added in 6E    |

**Channel planning — 2.4 GHz:** Only channels **1, 6, 11** are non-overlapping (US). Interference sources: microwaves, Bluetooth, baby monitors.

#### Cellular
| Gen | Speed | Notes |
|-----|-------|-------|
| 3G | ~2 Mbps | HSPA+ |
| 4G LTE | ~100 Mbps | VoLTE |
| 5G | ~10 Gbps theoretical | Sub-6 GHz (range) vs mmWave (speed/short range) |

**SIM/eSIM:** SIM = physical card; eSIM = embedded programmable; both identify subscriber on carrier network.

#### MDM and Synchronization
- **MDM:** Corporate management; enforces policies, remote wipe, app management
- **BYOD vs Corporate-owned:** BYOD = personal device, corporate policy; Corporate = company owns and controls fully
- **Data caps:** Metered connections limit sync; configure sync to Wi-Fi only when applicable
- **Sync targets:** Calendar, contacts, mail, cloud storage, business apps

### 1.4 Mobile Troubleshooting (5.4 — also applies here)
| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| No charge | Faulty cable, dirty port, bad battery | Clean port; swap cable; replace battery |
| Overheating | Background apps, charging while gaming | Close apps; remove case |
| Touchscreen unresponsive | Digitizer damage, freeze | Reboot; replace digitizer |
| No Wi-Fi | Wrong password, driver issue | Forget/rejoin; toggle airplane |
| Poor/no connectivity | Antenna disconnected (post-repair) | Reseat antenna cable |
| GPS inaccurate | A-GPS stale, indoors | High-accuracy mode; move outdoors |
| Bluetooth not pairing | Already connected elsewhere | Unpair; clear Bluetooth cache |
| Swollen battery | Li-Ion failure | Do not puncture; replace immediately — fire hazard |
| Liquid damage | Water ingress | Do not power on; dry thoroughly; rice myth — use silica gel |
| Cursor drift | Touch calibration issue | Recalibrate; replace digitizer |
| Stylus not working | Digitizer issue, pairing | Re-pair; check digitizer |
| Degraded performance | Low storage, background apps | Free storage (keep >10% free); factory reset |

---

## Domain 2 — Networking (23%)

### 2.1 Ports and Protocols
| Protocol | Port | Transport | Purpose |
|----------|------|-----------|---------|
| FTP | 20/21 | TCP | File transfer (unencrypted) |
| SSH/SFTP | 22 | TCP | Secure shell; secure file transfer |
| Telnet | 23 | TCP | Unencrypted remote shell — avoid |
| SMTP | 25 | TCP | Send email |
| DNS | 53 | TCP/UDP | Name resolution |
| DHCP | 67/68 | UDP | Dynamic IP assignment |
| HTTP | 80 | TCP | Unencrypted web |
| POP3 | 110 | TCP | Retrieve email (removes from server) |
| NetBIOS/NetBT | 137-139 | TCP/UDP | Windows legacy name resolution |
| IMAP | 143 | TCP | Retrieve email (leaves on server) |
| LDAP | 389 | TCP | Directory services |
| HTTPS | 443 | TCP | Encrypted web (TLS) |
| SMB/CIFS | 445 | TCP | Windows file/print sharing |
| RDP | 3389 | TCP | Remote Desktop Protocol |

**TCP vs UDP:** TCP = connection-oriented, reliable, ordered. UDP = connectionless, fast, no guarantee. DNS uses both; DNS queries = UDP, zone transfers = TCP.

### 2.2 Wireless Technologies
- **Frequencies:** 2.4 GHz (range, interference), 5 GHz (speed, less interference), 6 GHz (Wi-Fi 6E only — new clean spectrum)
- **Channel width:** 20/40/80/160 MHz — wider = faster but more interference
- **RFID:** Radio-Frequency Identification; passive (no battery) or active; used for asset tracking, access cards
- **NFC:** Subset of RFID; 13.56 MHz; range ~4 cm
- **Bluetooth classes:** Class 1 (~100m), Class 2 (~10m), Class 3 (~1m)

### 2.3 Server Roles and Internet Appliances
| Role | Purpose |
|------|---------|
| DNS | Resolves hostnames to IP addresses |
| DHCP | Assigns IP addresses dynamically |
| File share | Shared storage (SMB/NFS) |
| Print server | Manages network printer queues |
| Mail server | SMTP send; POP3/IMAP receive |
| Web server | HTTP/HTTPS content delivery |
| Syslog | Centralized log collection |
| NTP | Network Time Protocol; time synchronization |
| AAA | Authentication, Authorization, Accounting; RADIUS, TACACS+ |
| Database server | Structured data storage (SQL) |

**Internet appliances:**
- **Spam gateway:** Filters inbound email for spam/malware before it reaches mail server
- **UTM (Unified Threat Management):** Combined firewall + IDS/IPS + AV + content filter in one appliance
- **Load balancer:** Distributes traffic across multiple servers for redundancy and performance
- **Proxy server:** Intermediary for client requests; caches content; can filter/log

**Legacy/embedded systems:**
- **SCADA:** Supervisory Control and Data Acquisition; controls industrial/infrastructure systems; often air-gapped; patching is difficult
- **IoT devices:** Smart thermostats, cameras, sensors; often weak security; isolate on guest/VLAN

### 2.4 Network Configuration

#### DNS Record Types
| Record | Purpose |
|--------|---------|
| A | Hostname → IPv4 |
| AAAA | Hostname → IPv6 |
| CNAME | Alias to another hostname |
| MX | Mail server for domain |
| TXT | Text records — SPF, DKIM, DMARC, verification |
| PTR | Reverse DNS (IP → hostname) |
| NS | Authoritative name server |

**Email security DNS records:**
- **SPF (TXT):** Lists authorized mail servers for domain; prevents spoofing
- **DKIM (TXT):** Cryptographic signature on outgoing mail; verifies sender
- **DMARC (TXT):** Policy for what to do when SPF/DKIM fail; instructs receivers to quarantine/reject

#### DHCP Details
- **Lease:** Temporary IP assignment with expiry time
- **Reservation:** Permanent IP assignment tied to MAC address (for servers, printers)
- **Scope:** Range of IPs available for assignment (e.g., 192.168.1.100–192.168.1.200)
- **Exclusions:** IPs within scope that are excluded from assignment (for static devices)
- **DORA process:** Discover → Offer → Request → Acknowledge

#### VLANs
- Logically segments a physical network into separate broadcast domains
- Requires managed switch
- Traffic between VLANs requires a router (or Layer 3 switch)
- Common use: separate guest traffic, IoT, VoIP, management traffic

#### IP Addressing
**IPv4 private ranges (RFC 1918):**
```
10.0.0.0/8       — Class A
172.16.0.0/12    — Class B
192.168.0.0/16   — Class C
127.0.0.1        — Loopback
169.254.0.0/16   — APIPA (no DHCP response)
```

**Subnetting quick reference:**
| CIDR | Mask | Hosts |
|------|------|-------|
| /24 | 255.255.255.0 | 254 |
| /25 | 255.255.255.128 | 126 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /28 | 255.255.255.240 | 14 |
| /30 | 255.255.255.252 | 2 |

**IPv6:** 128-bit; `::1` loopback; `fe80::/10` link-local; no broadcast; NDP replaces ARP.
**APIPA:** 169.254.x.x — auto-assigned when DHCP fails; not routable.
**Static vs Dynamic:** Static = manually configured; Dynamic = DHCP assigned.

### 2.5 Network Hardware
| Device             | Layer    | Function                                                               |
| ------------------ | -------- | ---------------------------------------------------------------------- |
| Hub                | 1        | Broadcasts all traffic to all ports                                    |
| Switch (unmanaged) | 2        | Forwards by MAC; no config                                             |
| Switch (managed)   | 2        | VLAN, QoS, port mirroring, SNMP                                        |
| Router             | 3        | Routes by IP; connects networks                                        |
| Access Point       | 2        | Wireless to wired bridge                                               |
| Firewall           | 3–7      | Traffic filtering by rules                                             |
| Cable modem        | 1        | Converts coax signal to Ethernet                                       |
| DSL modem          | 1        | Converts phone line to Ethernet                                        |
| ONT                | 1        | Optical Network Terminal; fiber to Ethernet                            |
| NIC                | 2        | Network Interface Card; has MAC address                                |
| PoE switch         | 2        | Delivers power over Ethernet (802.3af=15.4W, 802.3at=30W, 802.3bt=90W) |
| PoE injector       | —        | Adds PoE to non-PoE switch port                                        |
| Patch panel        | Physical | Organizes cabling in rack; no active function                          |

### 2.6 SOHO Network Setup
```
ISP → Modem → Router (built-in switch + AP) → Devices
```
Key config steps: change default admin credentials, set WAN type, configure SSID/WPA3, set DHCP pool, configure DNS, enable firewall, disable UPnP.

### 2.7 Internet Connection Types and Network Types

**Internet connection types:**
| Type | Speed | Notes |
|------|-------|-------|
| Fiber | Up to 10 Gbps | Fastest; most reliable; expensive to deploy |
| Cable | 100 Mbps–1 Gbps | Coaxial; shared neighborhood bandwidth |
| DSL | 1–100 Mbps | Phone line; speed depends on distance from CO |
| Satellite | 25–100 Mbps (Starlink higher) | High latency (~600ms traditional; ~20–60ms LEO) |
| Cellular | 4G ~100 Mbps; 5G higher | Mobile broadband; hotspot |
| WISP | Varies | Wireless Internet Service Provider; fixed wireless via radio tower |

**Network types:**
| Type | Description |
|------|-------------|
| LAN | Local Area Network — single building/floor |
| WAN | Wide Area Network — connects multiple sites across distance |
| PAN | Personal Area Network — Bluetooth/USB; ~10m |
| MAN | Metropolitan Area Network — city-wide |
| SAN | Storage Area Network — high-speed dedicated storage network |
| WLAN | Wireless LAN — Wi-Fi |

### 2.8 Networking Tools
| Tool | Purpose |
|------|---------|
| Crimper | Terminates twisted-pair onto RJ-45/RJ-11 |
| Cable stripper | Removes outer jacket for termination |
| Punchdown tool | Seats wire into 110-block or keystone jack |
| Cable tester | Verifies continuity and correct pinout |
| Toner probe | Tone generator + probe; traces cables through walls |
| Wi-Fi analyzer | Shows SSIDs, channels, signal strength; identifies interference |
| Loopback plug | Tests NIC/port by looping TX to RX |
| Network tap | Passive inline device for packet capture; does not affect traffic |

**Cable categories:**
| Cat | Speed | Max Distance |
|-----|-------|--------------|
| Cat 5e | 1 Gbps | 100 m |
| Cat 6 | 10 Gbps | 55 m (10G) / 100 m (1G) |
| Cat 6A | 10 Gbps | 100 m (fully shielded) |
| Cat 8 | 25/40 Gbps | 30 m (data center) |

**Wiring standards:** T568A and T568B. Straight-through (same standard both ends) = PC to switch. Crossover (opposite ends) = switch to switch — mostly obsolete due to Auto-MDI-X.

**Fiber optic:**
- **Single-mode (SMF):** Narrow core (~9 µm); long distance; laser; yellow jacket typically
- **Multimode (MMF):** Wider core (~50/62.5 µm); shorter distance; LED; orange/aqua jacket

**Fiber connectors:**
| Connector | Type | Notes |
|-----------|------|-------|
| ST | Straight Tip | Bayonet twist-lock; older |
| SC | Subscriber Connector | Push-pull; square; common |
| LC | Lucent Connector | Small form factor; common in data centers |

**Coaxial:** F-type connector — used for cable TV/internet; threaded.

**VPN types:** Site-to-site (IPSec), Client VPN (OpenVPN/WireGuard/IKEv2), SSL/TLS (port 443), split vs full tunnel.

---

## Domain 3 — Hardware (25%)

### 3.1 Display Types and Attributes

**LCD panel types:**
| Type | Viewing Angle | Response Time | Color | Use |
|------|--------------|---------------|-------|-----|
| TN (Twisted Nematic) | Narrow | Fastest (<1ms) | Poor | Gaming (speed) |
| IPS (In-Plane Switching) | Wide | Moderate | Excellent | Professional, general use |
| VA (Vertical Alignment) | Wide | Moderate | Best contrast | Media consumption |

**Other display types:**
- **OLED:** Each pixel emits light; true black; flexible; risk of burn-in; used in phones/high-end monitors
- **Mini-LED:** Very small LEDs for backlighting; better local dimming than standard LCD

**Display attributes:**
| Attribute | Notes |
|-----------|-------|
| Resolution | Pixel count (e.g., 1920×1080); higher = sharper |
| Pixel density | PPI (pixels per inch); higher = sharper at same size |
| Refresh rate | Hz; 60Hz standard; 144/165Hz for gaming |
| Response time | ms; lower is better for motion clarity |
| Color gamut | sRGB, DCI-P3, Adobe RGB; wider = more colors |
| Brightness | Nits; higher for HDR or bright environments |

**Touch screen/digitizer:** Capacitive (most modern); resistive (older, pressure-based). Digitizer is the touch layer; separate from the display panel.

**Inverter:** Converts DC to AC for CCFL backlights in older LCD screens. If screen is dim but visible, suspect inverter failure.

### 3.2 Cables and Connectors

**Video cables:**
| Connector | Max Resolution | Audio | Notes |
|-----------|---------------|-------|-------|
| VGA | 1080p (analog) | No | Legacy; 15-pin D-sub |
| DVI-D | 2560×1600 | No | Digital only |
| DVI-I | 2560×1600 | No | Digital + analog |
| HDMI 1.4 | 4K@30Hz | Yes | Most common |
| HDMI 2.0 | 4K@60Hz | Yes | |
| HDMI 2.1 | 4K@144Hz/8K | Yes | Gaming monitors |
| DisplayPort 1.4 | 8K@60Hz | Yes | Daisy-chain support |
| USB-C (DP Alt) | Up to 8K | Yes | Requires DP Alt Mode |
| Thunderbolt 3/4 | 8K | Yes | USB-C connector |

**Hard drive cables:**
- **SATA:** 7-pin data connector; L-shaped; for HDD and SATA SSD
- **eSATA:** External SATA; hot-swap capable; faster than USB 2.0; no power delivery
- **M.2:** Edge connector; 2242/2260/2280 sizes; B-key (SATA), M-key (NVMe), B+M (both)

**Peripheral cables:**
- **USB 2.0:** 480 Mbps; black connector typically
- **USB 3.0/3.1 Gen 1:** 5 Gbps; blue connector; backward compatible
- **Serial:** DB9 (9-pin D-sub); legacy; used for console cables on network gear
- **Thunderbolt:** TB1/2 = Mini DisplayPort connector; TB3/4 = USB-C connector

**Connector types:**
| Connector | Use |
|-----------|-----|
| RJ11 | 6P2C; telephone; 2-wire |
| RJ45 | 8P8C; Ethernet; 8-wire |
| F-type | Coaxial; cable TV/internet; threaded |
| ST | Fiber; bayonet lock |
| SC | Fiber; push-pull; square |
| LC | Fiber; small form factor |
| Punchdown block | 110-block or 66-block; seats wire without stripping |
| MicroUSB | Older Android/peripherals |
| MiniUSB | Older cameras/devices |
| USB-C | Universal; reversible |
| Molex | 4-pin; legacy power for HDD/fans |
| Lightning | Apple pre-2023 |
| DB9 | Serial port; 9-pin |

**Adapters:** DVI-to-HDMI, DisplayPort-to-HDMI, USB-C-to-HDMI, Bluetooth USB adapter.

**Plenum-rated cable:** Fire-resistant jacket; required in air handling spaces (ceiling plenums); does not emit toxic fumes when burned.

### 3.3 RAM
| Type | Speed | Notes |
|------|-------|-------|
| DDR4 | 2133–3600+ MHz | Current mainstream; 288-pin DIMM |
| DDR5 | 4800–8000+ MHz | Newest; incompatible with DDR4 slots |
| SO-DIMM | Varies | Laptop; 260-pin (DDR4/5) |
| ECC | Varies | Error-correcting; server/workstation; detects and corrects single-bit errors |

**Channel config:** Single (1 stick) → Dual (2 matched sticks in A2+B2) → Quad (HEDT/server).
**XMP/EXPO:** Must enable in BIOS to run at rated speed; default is slow JEDEC baseline.

### 3.4 Storage Devices
| Type | Interface | Speed | Notes |
|------|-----------|-------|-------|
| HDD 3.5" | SATA | ~150 MB/s | Desktop; 5400/7200 RPM |
| HDD 2.5" | SATA | ~150 MB/s | Laptop |
| SATA SSD | SATA | ~550 MB/s | 2.5" or M.2 SATA |
| NVMe SSD | PCIe M.2 | 3500–7000+ MB/s | Fastest consumer storage |
| mSATA | SATA | ~550 MB/s | Legacy small form factor |
| SAS | SAS | ~1.2 GB/s | Enterprise; 10k/15k RPM; dual-port redundancy |
| eMMC | Integrated | ~400 MB/s | Budget devices; soldered |
| Flash drive | USB | Varies | Portable; FAT32/exFAT |
| Memory card | SD/microSD | Varies | Cameras, phones |
| Optical drive | SATA | — | CD/DVD/Blu-ray; largely obsolete |

**RAID levels:**
| RAID | Min Drives | Fault Tolerance | Notes |
|------|------------|-----------------|-------|
| 0 | 2 | None | Stripe — performance only |
| 1 | 2 | 1 drive | Mirror |
| 5 | 3 | 1 drive | Stripe + parity |
| 6 | 4 | 2 drives | Double parity |
| 10 | 4 | 1 per mirror pair | Mirror + stripe |

> RAID is NOT a backup. Does not protect against ransomware, accidental deletion, or fire/flood.

### 3.5 Motherboards, CPUs, and Add-on Cards

**Motherboard form factors:** ATX (305×244mm), Micro-ATX (244×244mm), Mini-ITX (170×170mm), E-ATX (>305mm).

**Key connectors:** PCI, PCIe (x1/x4/x8/x16), SATA, eSATA, M.2, power connectors, headers (USB, audio, front panel).

**CPU sockets:** Intel LGA (pins on board); AMD AM5 (pins on CPU). Must match board generation.

**BIOS/UEFI settings:**
| Setting | Purpose |
|---------|---------|
| Boot order | Device priority for OS load |
| Secure Boot | Blocks unsigned bootloaders; required for Win 11 |
| TPM | Required for BitLocker and Windows 11 |
| VT-x / AMD-V | Enable for hypervisors |
| XMP/EXPO | Enables rated RAM speed |
| AHCI | SSD TRIM + hot-swap support |
| Fan settings | Speed curves; thermal control |
| Temperature monitoring | CPU/system thermal readings |
| Boot password / BIOS password | Pre-boot security |

**CPU architecture:**
- **x86/x64:** Standard PC architecture; Intel and AMD
- **ARM:** Mobile devices; low power; Apple Silicon; Qualcomm Snapdragon
- **Core configurations:** Single, dual, quad, hexa, octa-core; HyperThreading = 2 threads/core

**Expansion cards:** Sound card, video/GPU (PCIe x16), capture card, NIC (PCIe x1), Wi-Fi card.

**Cooling:** Fans, heatsink (HSF), thermal paste (pea-sized center; spread by mounting pressure), liquid AIO, custom loop.

**Encryption:**
- **TPM (Trusted Platform Module):** Chip on motherboard; stores encryption keys; required for BitLocker and Windows 11
- **HSM (Hardware Security Module):** External or PCIe card; higher security than TPM; enterprise key management

### 3.6 Power Supplies
| Connector | Use |
|-----------|-----|
| 24-pin ATX | Motherboard main power |
| 8-pin EPS (4+4) | CPU power |
| PCIe 6/8/16-pin | GPU power |
| SATA power | Drives, fans |
| Molex | Legacy drives, adapters |

**Voltages:** 3.3V (RAM, chipset), 5V (USB, older drives), 12V (CPU, GPU, motors).
**80 Plus ratings:** White → Bronze → Silver → Gold → Platinum → Titanium (efficiency).
**Modular PSU:** Cables detach; better airflow. **Redundant PSU:** Two PSUs; hot-swap; server use.
**Wattage:** Sum TDP of all components + 20% headroom.

### 3.7 Printers — Configuration and Deployment
- **PCL (Printer Command Language):** HP standard; faster for text/office documents
- **PostScript:** Adobe standard; better for graphics/design; more CPU intensive
- **Drivers:** Must match OS (32-bit vs 64-bit); use manufacturer drivers
- **Firmware:** Keep updated for security and bug fixes
- **Connectivity:** USB (direct), Ethernet (network), Wireless (Wi-Fi)
- **Shared printer:** Host PC shares via OS; all traffic routes through host
- **Print server:** Dedicated hardware or software; accepts jobs independently of host PC
- **Security:** User authentication, badge/PIN release (secured prints), audit logs, disable unused protocols
- **Network scan services:** Scan to email, scan to SMB share, scan to cloud
- **ADF (Automatic Document Feeder):** Multi-page scanning without manual intervention
- **Flatbed scanner:** Single page; better quality for fragile/bound documents
- **Configuration settings:** Duplex (2-sided), orientation (portrait/landscape), tray selection, quality (DPI)

### 3.8 Printer Maintenance by Type

**Laser printer print process (memorize order):**
```
1. Processing  — RIP converts data to bitmap
2. Charging    — Drum charged ~-600V (primary corona/roller)
3. Exposing    — Laser neutralizes charged areas on drum
4. Developing  — Toner (negatively charged) attracted to neutral areas
5. Transferring — Transfer corona/belt moves toner to paper
6. Fusing      — Heat (~180°C) + pressure bonds toner to paper
7. Cleaning    — Blade removes residual toner from drum
```

**Laser maintenance:** Replace toner, apply maintenance kit (fuser + rollers), calibrate, clean drum/paper path.

**Inkjet maintenance:** Clean printheads (automated or manual), replace cartridges, calibrate color, clear jams, replace rollers/feeder.

**Thermal maintenance:** Replace thermal paper roll, clean heating element with isopropyl alcohol, remove debris from feed path. Note: thermal printers use no ink — paper is heat-sensitive.

**Impact (dot matrix) maintenance:** Replace ribbon cartridge, replace printhead (pins), replace paper (often multipart NCR forms). Still used for triplicate forms (shipping, medical, receipts requiring carbon copies).

---

## Domain 4 — Virtualization and Cloud Computing (11%)

### 4.1 Virtualization Concepts
| Term | Definition |
|------|-----------|
| Hypervisor Type 1 | Bare-metal; runs directly on hardware; ESXi, Hyper-V, KVM |
| Hypervisor Type 2 | Hosted; runs on OS; VirtualBox, VMware Workstation, Parallels |
| VM | Emulated computer with vCPU, vRAM, virtual disk, virtual NIC |
| Snapshot | Point-in-time VM state; enables rollback |
| Container | Lightweight isolated process; shares host OS kernel; Docker; faster than VM |
| VDI | Virtual Desktop Infrastructure; centralized VMs; thin clients connect |
| Application virtualization | App runs on server, streamed to client; Citrix, App-V |
| Sandbox | Isolated VM for testing/malware analysis |

**VM network modes:**
- **Bridged:** VM gets own IP on LAN; appears as separate device
- **NAT:** VM shares host IP; can reach internet; not directly accessible from LAN
- **Host-only:** VM talks only to host; completely isolated

**VM use cases:** Sandbox (safe testing/malware), test development (without affecting production), legacy OS/app compatibility, cross-platform virtualization.

**Requirements:** CPU must support Intel VT-x or AMD-V (enable in BIOS); adequate RAM and storage.

### 4.2 Cloud Computing
**Service models:**
| Model | Provider Manages | Customer Manages | Examples |
|-------|-----------------|-----------------|---------|
| IaaS | Hardware, network, hypervisor | OS, apps, data | AWS EC2, Azure VMs |
| PaaS | Everything through runtime | Apps and data | Heroku, Google App Engine |
| SaaS | Everything | Account/settings | Microsoft 365, Salesforce |

**Deployment models:** Public (shared, provider-managed), Private (dedicated, on-prem or hosted), Hybrid (mix), Community (shared among similar orgs).

**Cloud characteristics (NIST):**
1. On-demand self-service
2. Broad network access
3. Resource pooling (multi-tenant)
4. Rapid elasticity
5. Measured service (pay-per-use)

**Additional cloud terms:**
- **Shared resources:** Multi-tenancy; logical isolation between customers
- **Dedicated resources:** Single-tenant; higher cost; better compliance
- **Metered utilization:** Pay for what you use; ingress (data in) often free; egress (data out) charged
- **Elasticity:** Auto-scale up/down based on demand
- **File synchronization:** Cloud storage sync (OneDrive, Google Drive, Dropbox)

---

## Domain 5 — Hardware and Network Troubleshooting (28%)

### 5.1 Motherboard, RAM, CPU, Power Symptoms
| Symptom | Likely Cause |
|---------|-------------|
| POST beep codes | Hardware failure at POST; varies by BIOS vendor |
| Blank screen / no POST | GPU, RAM, or PSU failure |
| No power | PSU, outlet, power cable, power button header |
| Overheating | Fan failure, blocked airflow, dried thermal paste |
| Burning smell | Shorted component, failed capacitor |
| Random shutdown | Thermal protection, failing PSU |
| Sluggish performance | Thermal throttling, failing HDD, low RAM |
| Application crashes | Bad RAM, driver conflict |
| Unusual noise | Failing fan bearing, coil whine (PSU/GPU) |
| Capacitor swelling | Bad motherboard capacitors; visible bulging; replace board |
| Inaccurate system date/time | Dead CMOS battery (CR2032); replace |

### 5.2 Drive and RAID Symptoms
| Symptom | Cause |
|---------|-------|
| Grinding/clicking noise | HDD mechanical failure — back up immediately |
| LED status indicators | RAID controller status; solid = OK; flashing = degraded/rebuilding |
| Bootable device not found | Failed drive, wrong boot order, corrupted BOM |
| S.M.A.R.T. failure | Drive reporting imminent failure via built-in diagnostics |
| Extended read/write times | Failing drive; bad sectors |
| Low IOPS | HDD degradation; SATA vs NVMe mismatch expectations |
| Missing drive in OS | Driver issue, failed drive, loose cable |
| RAID failure | Too many drives failed; array offline |
| Array missing | Controller failure; cable issue |
| Audible alarms | RAID controller beeping on degraded/failed array |
| Data loss/corruption | Drive failure, filesystem error; run chkdsk/fsck |

### 5.3 Video and Display Symptoms
| Symptom | Cause | Fix |
|---------|-------|-----|
| No display | Wrong input source, cable, GPU | Check input source; reseat GPU; swap cable |
| Physical cabling issues | Loose/damaged connector | Reseat or replace cable |
| Fuzzy image | Wrong resolution, bad cable | Set native resolution; replace cable |
| Display burn-in | Static image on OLED/plasma too long | Use screensaver; varies by panel — may be permanent |
| Dead pixels | Manufacturing defect or damage | Check warranty policy; may require replacement |
| Flashing screen | Loose cable, driver issue, failing panel | Reseat cable; update/rollback driver |
| Incorrect color display | Wrong color profile, driver | Calibrate; update driver |
| Dim image | Failing backlight or inverter (CCFL) | Replace inverter/backlight |
| Distorted image | Incorrect resolution/refresh, cable issue | Check display settings |
| Sizing/position issues | Wrong resolution or display settings | Adjust in display settings |
| Burnt-out projector bulb | Lamp end of life | Replace projector lamp |
| Intermittent projector shutdown | Overheating (blocked vents) | Clean vents; check fan |

### 5.4 Mobile Device Symptoms
Covered in Domain 1.4 above.

### 5.5 Network Troubleshooting Symptoms
| Symptom | Cause | Fix |
|---------|-------|-----|
| Intermittent wireless | Channel congestion, interference, weak signal | Change channel; move AP; check for interference |
| Slow network speeds | Duplex mismatch, bad cable, congestion | Check NIC settings; replace cable; check utilization |
| Limited connectivity | DHCP failure, IP conflict | ipconfig /renew; check for duplicate IPs |
| APIPA (169.254.x.x) | DHCP unreachable | Check DHCP server; check cable |
| Jitter | Inconsistent packet delay; VoIP/gaming issue | Check for congestion; QoS configuration |
| Poor VoIP quality | Jitter, latency, packet loss | Implement QoS; prioritize voice traffic; check bandwidth |
| Port flapping | Physical layer issue; cable or NIC problem | Replace cable; check SFP/NIC |
| High latency | Congestion, distance, satellite link | Check path with tracert; upgrade link |
| External interference | Microwave, Bluetooth, neighboring Wi-Fi | Change 2.4 GHz channel; move to 5 GHz |
| Authentication failures | Wrong credentials, RADIUS issue, expired cert | Verify credentials; check RADIUS server; check cert |
| Intermittent internet | ISP issue, modem/router rebooting | Check modem logs; contact ISP |

### 5.6 Printer Troubleshooting
| Symptom | Cause | Fix |
|---------|-------|-----|
| Lines down pages | Dirty drum or toner cartridge strip | Clean or replace drum/cartridge |
| Garbled print | Corrupt driver, wrong PCL/PostScript | Reinstall driver; check page language setting |
| Paper jam | Worn rollers, wrong paper type, debris | Clear jam; clean rollers; check paper spec |
| Faded prints | Low toner (laser); low ink/clogged head (inkjet) | Replace toner/cartridge; run head cleaning |
| Paper not feeding | Worn pickup rollers | Clean or replace rollers |
| Multipage misfeed | Worn separator pad | Replace separator pad |
| Multiple jobs in queue | Stuck print job | Clear queue; restart spooler |
| Speckling on pages | Dirty drum or excess toner | Clean drum; check toner cartridge seating |
| Double/echo images | Fuser not hot enough; dirty drum | Replace fuser; replace drum |
| Grinding noise | Foreign object; worn rollers | Inspect and clear; replace rollers |
| Finishing issues | Staple jam, hole punch misaligned | Clear finisher; check paper alignment |
| Wrong page orientation | Driver setting | Check print dialog orientation |
| Tray not recognized | Tray not seated; driver issue | Reseat tray; reinstall driver |
| Connectivity issues | IP changed, offline | Set static IP for printer; check network |
| Frozen print queue | Stuck job locks queue | Restart Print Spooler service |

```cmd
:: Restart print spooler
net stop spooler
del /Q /F /S "%systemroot%\System32\spool\PRINTERS\*.*"
net start spooler
```

---

## Key Diagnostic Commands
```cmd
ipconfig /all          :: Full IP config
ipconfig /release      :: Release DHCP lease
ipconfig /renew        :: Renew DHCP lease
ipconfig /flushdns     :: Clear DNS cache
ping 127.0.0.1         :: Test local TCP/IP stack
ping <gateway>         :: Test local network
ping 8.8.8.8           :: Test internet (no DNS)
ping google.com        :: Test DNS resolution
tracert google.com     :: Trace route to host
nslookup google.com    :: DNS query
arp -a                 :: View ARP cache
netstat -an            :: Active connections
route print            :: Routing table
```

---

## Quick Reference — Exam Gotchas

- **Core 1 passing score: 675** (Core 2: 700) — different scores for each exam
- **APIPA 169.254.x.x** = DHCP server unreachable
- **Loopback 127.0.0.1** = local TCP/IP stack test
- **WEP** = broken; never use
- **SSID hiding** = security through obscurity; not real security
- **RAID 0** = no fault tolerance; striping only
- **RAID 5** = minimum 3 drives; 1 drive worth of capacity to parity
- **NVMe ≠ M.2** — M.2 is the form factor; can be SATA or NVMe
- **DDR4 and DDR5 slots are NOT interchangeable**
- **Laser print order:** Processing → Charging → Exposing → Developing → Transferring → Fusing → Cleaning
- **POST beep codes vary by BIOS vendor** — always check documentation
- **Type 1 hypervisor** = bare-metal (ESXi, Hyper-V, KVM)
- **Type 2 hypervisor** = hosted (VirtualBox, VMware Workstation)
- **Containers share host OS kernel** — lighter than VMs
- **Inverter** = needed for CCFL backlit LCDs; not needed for LED/OLED
- **Dim screen but image visible** = suspect inverter (older laptops)
- **Capacitor swelling** = replace motherboard
- **CMOS battery** = CR2032; replace when clock resets on reboot
- **Plenum cable** = required in air handling spaces; fire-safe jacket
- **Single-mode fiber** = long distance; yellow; laser
- **Multimode fiber** = shorter distance; orange/aqua; LED
- **PoE standards:** 802.3af=15.4W, 802.3at=30W, 802.3bt=90W
- **Jitter** = inconsistent packet delay; worst enemy of VoIP
- **S.M.A.R.T. failure** = drive reporting imminent failure; back up immediately
- **Toner cleaning** = cold water only; hot water permanently fuses toner
- **Swollen battery** = replace immediately; fire hazard; do not puncture

---

## Related Notes
- CompTIA-Aplus-Core2-220-1202
- Security-Plus-SY0-701
- Networking-Basics
- OSI-Model
- TCP-IP-Fundamentals
- Common-Ports

## References
- CompTIA A+ Core 1 (220-1201) V15 Official Exam Objectives
- Professor Messer A+ Course Notes: https://www.professormesser.com
- Mike Meyers CompTIA A+ Certification Study Guide

## See Also
- CompTIA-Aplus-Core2-220-1202
- Security-Plus-SY0-701
- Networking-Basics
- Common-Ports

---

## Exam Focus Callouts — Core 1

> [!important] MUST MEMORIZE — Laser Print Order
> **Processing → Charging → Exposing → Developing → Transferring → Fusing → Cleaning**
> - ==Charging:== drum = ~-600V (primary corona/roller)
> - ==Exposing:== laser neutralizes charged areas on drum
> - ==Developing:== toner (negatively charged) attaches to neutral areas
> - ==Transferring:== toner moves from drum to paper (transfer corona/belt)
> - ==Fusing:== heat (~180°C) + pressure bonds toner permanently
> - ==Cleaning:== blade removes residual toner
> - **Cold water only** to clean toner — hot water permanently fuses it

> [!important] MUST MEMORIZE — RAID Quick Reference
> | RAID | Min Drives | Fault Tolerance | Key Fact |
> |------|------------|-----------------|---------|
> | ==0== | 2 | **None** | Stripe only — performance, no protection |
> | ==1== | 2 | 1 drive | Mirror — 50% storage efficiency |
> | ==5== | **3** | 1 drive | Stripe + parity — balance of all |
> | ==6== | 4 | **2 drives** | Double parity |
> | ==10== | 4 | 1 per mirror pair | Mirror + stripe |
> - **RAID is NOT a backup** — does not protect against ransomware or deletion

> [!important] MUST MEMORIZE — Ports and Protocols
> | Port | Protocol | Remember |
> |------|----------|---------|
> | ==20/21== | FTP | 20=data, 21=control |
> | ==22== | SSH / SFTP | Secure shell AND secure file transfer |
> | ==23== | Telnet | Unencrypted — never use |
> | ==25== | SMTP | Send mail |
> | ==53== | DNS | TCP and UDP |
> | ==67/68== | DHCP | 67=server, 68=client |
> | ==80== | HTTP | Unencrypted web |
> | ==110== | POP3 | Retrieves and removes from server |
> | ==143== | IMAP | Retrieves; leaves on server |
> | ==443== | HTTPS | Encrypted web |
> | ==445== | SMB | Windows file/print sharing |
> | ==3389== | RDP | Remote Desktop |
> | 548 | TCP | AFP

> [!important] MUST MEMORIZE — Wi-Fi Standards
> | Standard | Freq | Max Speed | Key Fact |
> |----------|------|-----------|---------|
> | ==802.11a== | 5 GHz | 54 Mbps | Less interference |
> | ==802.11b== | 2.4 GHz | 11 Mbps | Longest range; slowest |
> | ==802.11g== | 2.4 GHz | 54 Mbps | Backward compat with b |
> | ==802.11n (Wi-Fi 4)== | 2.4/5 GHz | 600 Mbps | MIMO; first dual-band |
> | ==802.11ac (Wi-Fi 5)== | 5 GHz | 3.5 Gbps | MU-MIMO; beamforming |
> | ==802.11ax (Wi-Fi 6/6E)== | 2.4/5/6 GHz | 9.6 Gbps | OFDMA; adds 6 GHz band |
> - 2.4 GHz non-overlapping channels (US): **1, 6, 11 only**

> [!tip] EXAM TRAPS — Common Wrong Answer Choices
> - ==NVMe ≠ M.2== — M.2 is the **form factor**; NVMe is the **protocol**. M.2 can also be SATA.
> - ==DDR4 and DDR5 are NOT interchangeable== — different notch positions
> - ==Type 1 hypervisor== = bare-metal = ESXi/Hyper-V/KVM (no host OS)
> - ==Type 2 hypervisor== = hosted = VirtualBox/VMware Workstation (runs on top of OS)
> - ==Containers share host OS kernel== — lighter than VMs; Docker is the example
> - ==APIPA 169.254.x.x== = DHCP server unreachable (not a valid routable address)
> - ==SSID hiding== = security through obscurity — NOT a real security control
> - ==RAID 0 has NO fault tolerance== — striping only; one drive fails = all data lost
> - ==Inverter== = only in older CCFL-backlit LCDs; not in LED or OLED displays
> - ==Capacitor swelling== = replace the motherboard; not repairable
> - ==CMOS battery (CR2032)== = replace when clock resets on every reboot
> - ==Single-mode fiber== = yellow jacket; laser; long distance
> - ==Multimode fiber== = orange/aqua jacket; LED; shorter distance
> - ==PoE 802.3af== = 15.4W; ==802.3at== = 30W; ==802.3bt== = 90W
> - ==Jitter== = inconsistent packet delay = enemy of VoIP quality
> - ==Swollen battery== = fire hazard; replace immediately; do not puncture
> - ==Plenum cable== = required in air handling spaces; fire-resistant jacket

> [!warning] HIGH-FREQUENCY EXAM TOPICS — Core 1
> These appear repeatedly across CompTIA A+ practice exams:
> 1. ==Laser printer step order== — always tested; know all 7 steps
> 2. ==RAID levels== — minimum drives, fault tolerance, use cases
> 3. ==TCP/UDP port numbers== — FTP, SSH, SMTP, DNS, DHCP, HTTP/S, POP3, IMAP, RDP
> 4. ==Wi-Fi standards== — frequencies and max speeds
> 5. ==IPv4 private ranges and APIPA== — what 169.254.x.x means
> 6. ==POST troubleshooting== — no display, beep codes, no power scenarios
> 7. ==Fiber connector types== — ST vs SC vs LC; single-mode vs multimode
> 8. ==Mobile troubleshooting== — swollen battery, digitizer, A-GPS, NFC range
> 9. ==Cloud service models== — IaaS/PaaS/SaaS; who manages what
> 10. ==Network troubleshooting commands== — ipconfig, ping, tracert, nslookup

---
<- [[Certs-MOC]]