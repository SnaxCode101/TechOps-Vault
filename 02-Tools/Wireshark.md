---
title: Wireshark
tags:
  - tool
  - network
  - packet-capture
  - traffic-analysis
  - forensics
  - foundational
category: network
platform: cross-platform
install: https://www.wireshark.org/download.html / apt install wireshark
created: 2026-03-23
updated: 2026-04-04
difficulty: foundational
---

# 🛠️ Wireshark

## What It Is
Wireshark is an open-source network protocol analyzer (packet sniffer). It captures live network traffic or reads saved capture files (`.pcap`, `.pcapng`) and displays packet-level details for every layer of the OSI model. It is used for network troubleshooting, traffic analysis, protocol learning, and forensic investigation.

## Installation
```bash
# Debian/Ubuntu
sudo apt install wireshark

# macOS
brew install --cask wireshark

# Windows / other: https://www.wireshark.org/download.html
```

> On Linux: add your user to the `wireshark` group to capture without root:
> `sudo usermod -aG wireshark $USER` (then log out and back in)

## Basic Syntax (CLI: tshark)
```bash
# Wireshark is primarily GUI-based; tshark is the CLI equivalent
tshark -i eth0                        # Capture on interface eth0
tshark -r capture.pcap                # Read from file
tshark -i eth0 -w output.pcap         # Capture and write to file
tshark -i eth0 -f "tcp port 80"       # Capture with BPF filter
tshark -r capture.pcap -Y "http"      # Apply display filter when reading
```

## Common Use Cases

### Capture live traffic on a specific interface
Open Wireshark → select interface → click the blue shark fin to start.

### Read a .pcap file
`File → Open` or drag the `.pcap` file into the Wireshark window.

### Follow a TCP stream (reconstruct conversation)
Right-click a packet → **Follow → TCP Stream** — reassembles the full conversation in plaintext (if unencrypted).

### Export objects (files transferred in HTTP, SMB, etc.)
`File → Export Objects → HTTP` (or SMB, DICOM, etc.) — extracts files from the capture.

### Filter by IP address
```
ip.addr == 192.168.1.10
ip.src == 192.168.1.10
ip.dst == 192.168.1.10
```

### Filter by protocol
```
tcp
udp
dns
http
icmp
arp
tls
```

### Filter by port
```
tcp.port == 443
tcp.dstport == 80
udp.port == 53
```

### Find credentials in cleartext protocols
Apply display filter: `ftp || http || telnet` → Follow stream to read credentials.

### Detect ARP spoofing
```
arp.duplicate-address-detected
```
Or look for multiple ARP replies mapping the same IP to different MAC addresses.

## Important Display Filters (Wireshark syntax)
| Filter | Description |
|---|---|
| `ip.addr == X.X.X.X` | Traffic to or from an IP |
| `tcp.port == N` | TCP traffic on port N |
| `http.request.method == "POST"` | HTTP POST requests only |
| `dns.qry.name contains "google"` | DNS queries containing "google" |
| `frame contains "password"` | Frames with the string "password" |
| `!(arp or icmp)` | Exclude ARP and ICMP noise |
| `tcp.flags.syn == 1 && tcp.flags.ack == 0` | SYN packets only (port scan detection) |
| `tcp.analysis.retransmission` | TCP retransmissions |
| `tls.handshake.type == 1` | TLS Client Hello messages |

> **Note:** Wireshark display filters use a different syntax than BPF (Berkeley Packet Filter) capture filters.

## Output Interpretation
- **Info column:** quick summary of each packet's purpose
- **Packet Details pane:** expandable OSI layer breakdown
- **Packet Bytes pane:** raw hex and ASCII representation
- Colored rows indicate protocol types (default coloring rules can be customized)

## Opsec / Legal Notes
> ⚠️ Capturing network traffic without authorization is illegal. Only capture on networks you own or have explicit permission to monitor.
- On shared networks, promiscuous mode may capture traffic not addressed to your machine.
- Encrypted traffic (TLS/HTTPS) requires the private key or pre-master secret log to decrypt.
- Captures may contain sensitive credentials and PII — handle `.pcap` files securely.

## Related Tools
- [[Nmap]] — discover hosts and services; complement with packet capture
- tcpdump — lightweight CLI packet capture (outputs `.pcap` Wireshark can read)
- tshark — CLI version of Wireshark

## Related Notes
- OSI-Model
- Networking-Basics

## References
- Official docs: https://www.wireshark.org/docs/
- Display filter reference: https://www.wireshark.org/docs/dfref/


## See Also

- Networking-Basics
- TCP-IP-Fundamentals
- OSI-Model
- Incident-Response
- Common-Ports

---
<- [[Tools-MOC]]