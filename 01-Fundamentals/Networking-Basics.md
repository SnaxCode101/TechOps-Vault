---
title: Networking Basics
tags:
  - fundamentals
  - networking
  - foundational
  - dns
  - dhcp
  - vlan
  - firewall
  - ntp
topic: Networking
difficulty: foundational
created: 2026-03-23
updated: 2026-04-02
source: RFC 791 (IPv4), RFC 793 (TCP), RFC 1918 (private addressing), RFC 2131 (DHCP), RFC 5905 (NTP), CompTIA Network+
---

# 🌐 Networking Basics

## Overview
Networking fundamentals cover the addressing, protocols, and services that underpin all modern digital communication. This note goes beyond theory — it covers the practical admin tasks you will perform on real systems: configuring DNS zones, scoping DHCP, segmenting with VLANs, writing firewall rules, and keeping time synchronized across hosts.

## Why It Matters in Security
You cannot detect, block, or exploit traffic you do not understand. Every packet capture, firewall rule, recon scan, and lateral movement technique depends on fluency in these fundamentals. Time sync errors break Kerberos. Flat networks enable lateral movement. Misconfigured DHCP gives attackers a foothold. DNS is the internet's phonebook — and its most-abused protocol.

---

## IP Addressing

### IPv4
32-bit address written as four octets. Divided into network and host portions by a subnet mask.

**Private ranges (RFC 1918) — not routable on the public internet:**

| Range | CIDR | Usable Hosts |
|---|---|---|
| 10.0.0.0 – 10.255.255.255 | /8 | ~16.7 million |
| 172.16.0.0 – 172.31.255.255 | /12 | ~1 million |
| 192.168.0.0 – 192.168.255.255 | /16 | ~65,534 |

**Special addresses:**
- `127.0.0.1` — loopback (localhost)
- `169.254.x.x` — APIPA (link-local, assigned when DHCP fails)
- `255.255.255.255` — limited broadcast (not forwarded by routers)

### IPv6
128-bit address in hexadecimal groups separated by colons. `::` compresses consecutive all-zero groups.
- `::1` — loopback
- `fe80::/10` — link-local (not routed beyond the local link)
- `2001:db8::/32` — documentation prefix (RFC 3849, not routable)

### Subnetting Quick Reference

| CIDR | Subnet Mask | Usable Hosts | Common Use |
|---|---|---|---|
| /24 | 255.255.255.0 | 254 | Standard LAN segment |
| /25 | 255.255.255.128 | 126 | Split /24 into two halves |
| /26 | 255.255.255.192 | 62 | Small department |
| /27 | 255.255.255.224 | 30 | Small team / DMZ |
| /30 | 255.255.255.252 | 2 | Point-to-point links |
| /32 | 255.255.255.255 | 1 (host route) | Loopback, static host route |

**Formula:** Usable hosts = 2^(32−CIDR) − 2 (subtract network and broadcast addresses)

---

## TCP vs UDP

| Property | TCP | UDP |
|---|---|---|
| Connection | 3-way handshake (SYN → SYN-ACK → ACK) | Connectionless |
| Delivery | Guaranteed, ordered, retransmitted on loss | Best-effort, no guarantee |
| Speed | Slower (overhead) | Faster |
| Use cases | HTTP/S, SSH, SMTP, RDP, FTP | DNS, DHCP, VoIP, NTP, SNMP |

**TCP 3-Way Handshake:**
```
Client  →  SYN          →  Server
Client  ←  SYN-ACK      ←  Server
Client  →  ACK          →  Server
[Connection established]
```
**Security note:** Half-open SYN flood attacks exploit this — attacker sends SYN but never completes the handshake, exhausting server connection table.

---

## Key Protocols Reference

| Protocol | Port(s) | Transport | Notes |
|---|---|---|---|
| HTTP | 80 | TCP | Unencrypted web |
| HTTPS | 443 | TCP | TLS-encrypted web |
| DNS | 53 | UDP/TCP | UDP for queries; TCP for zone transfers and large responses |
| SSH | 22 | TCP | Encrypted remote shell |
| FTP | 20 (data), 21 (control) | TCP | Unencrypted; avoid in production |
| SFTP | 22 | TCP | File transfer over SSH |
| SMTP | 25 (server-to-server), 587 (client submission) | TCP | Email |
| RDP | 3389 | TCP | Windows Remote Desktop |
| SMB | 445 | TCP | Windows file/printer sharing |
| SNMP | 161 (queries), 162 (traps) | UDP | Network device monitoring |
| NTP | 123 | UDP | Time synchronization |
| DHCP | 67 (server), 68 (client) | UDP | Dynamic IP assignment |
| ICMP | — | N/A (Layer 3) | Ping, traceroute — no port number |
| ARP | — | N/A (Layer 2) | IP-to-MAC resolution |

---

## DNS — Domain Name System

DNS maps human-readable names to IP addresses (and much more). Defined in RFC 1034/1035. Port 53 UDP (queries) and TCP (zone transfers, responses >512 bytes, DNS over TCP fallback).

### DNS Record Types

| Record | Purpose | Example |
|---|---|---|
| `A` | Hostname → IPv4 address | `web.example.com. 300 IN A 93.184.216.34` |
| `AAAA` | Hostname → IPv6 address | `web.example.com. 300 IN AAAA 2606:2800::1` |
| `CNAME` | Alias → canonical name | `www.example.com. CNAME web.example.com.` |
| `MX` | Mail exchange server (with priority) | `example.com. MX 10 mail.example.com.` |
| `NS` | Nameserver for the zone | `example.com. NS ns1.example.com.` |
| `PTR` | IP → hostname (reverse DNS) | `34.216.184.93.in-addr.arpa. PTR web.example.com.` |
| `SOA` | Start of Authority — zone metadata | Serial, refresh, retry, expire, TTL |
| `TXT` | Arbitrary text — SPF, DKIM, domain verification | `v=spf1 include:_spf.google.com ~all` |
| `SRV` | Service location (host + port) | Used by SIP, XMPP, LDAP, Kerberos |

### DNS Zones

A **zone** is the portion of the DNS namespace that a particular nameserver is authoritative for.

**Forward zone** — maps names to IPs (standard lookups).
**Reverse zone** — maps IPs to names (PTR records). Reverse zone name format: octets reversed + `.in-addr.arpa`. For `192.168.1.0/24` the reverse zone is `1.168.192.in-addr.arpa`.

**Zone file structure (BIND format):**
```
$TTL 86400
@   IN  SOA  ns1.example.com. admin.example.com. (
            2026040201  ; Serial (YYYYMMDDnn)
            3600        ; Refresh
            900         ; Retry
            604800      ; Expire
            300 )       ; Minimum TTL

; Nameservers
@   IN  NS   ns1.example.com.
@   IN  NS   ns2.example.com.

; A records
@       IN  A    93.184.216.34
www     IN  A    93.184.216.34
mail    IN  A    93.184.216.50

; MX record
@   IN  MX  10  mail.example.com.

; TXT (SPF)
@   IN  TXT "v=spf1 a mx ~all"
```

**Reverse zone file (`1.168.192.in-addr.arpa`):**
```
$TTL 86400
@   IN  SOA  ns1.example.com. admin.example.com. (
            2026040201 3600 900 604800 300 )
@   IN  NS   ns1.example.com.

10  IN  PTR  server1.example.com.
20  IN  PTR  server2.example.com.
```

**Zone transfer (AXFR):** Full copy of a zone from primary to secondary nameserver. TCP port 53. Should be restricted to known secondary nameservers only — unrestricted AXFR leaks all DNS records.

### DNS Resolution Flow
1. Client checks local cache (and `/etc/hosts`)
2. Query goes to configured resolver (stub resolver → recursive resolver)
3. Recursive resolver queries: root servers → TLD nameservers (`.com`, `.org`) → authoritative nameserver
4. Authoritative nameserver returns answer
5. Response cached by resolver for TTL duration; returned to client

### DNS Tools

```bash
# Forward lookup
dig example.com A
dig example.com MX
dig example.com ANY          # All record types (may be refused)

# Reverse lookup
dig -x 93.184.216.34

# Query specific nameserver
dig @8.8.8.8 example.com A

# Zone transfer attempt (AXFR)
dig @ns1.example.com example.com AXFR

# Check SOA / serial
dig example.com SOA

# Trace resolution path
dig +trace example.com

# Windows equivalent
nslookup example.com
nslookup -type=MX example.com 8.8.8.8
```

**Security relevance:**
- DNS tunneling encodes data in subdomains (e.g., `c2data.attacker.com`) — exfiltrates data through DNS queries
- DNS cache poisoning injects false records to redirect traffic
- AXFR against misconfigured nameservers reveals full zone (all hostnames, IPs, mail infrastructure)
- Subdomain enumeration via brute-force or certificate transparency logs maps attack surface

---

## DHCP — Dynamic Host Configuration Protocol

Defined in RFC 2131. DHCP automates IP configuration for clients on a network. Operates on UDP port 67 (server) and 68 (client).

### The DORA Process (Lease Acquisition)

```
Client                          Server
  |                               |
  |-- DISCOVER (broadcast) -----> |  "Who can give me an IP?"
  |                               |
  |<-- OFFER (unicast/bcast) ---- |  "I offer 192.168.1.50 for 24h"
  |                               |
  |-- REQUEST (broadcast) ------> |  "I accept 192.168.1.50"
  |                               |
  |<-- ACK (unicast/bcast) ------ |  "Confirmed. Lease granted."
```

If the lease is 50% expired, client attempts renewal directly (unicast) to the original server. At 87.5% expiry, client broadcasts to any DHCP server.

### DHCP Scope Configuration (Windows Server example)
Key parameters configured in a scope:
- **IP range** — e.g., `192.168.1.100 – 192.168.1.200`
- **Exclusions** — IPs reserved for static assignment within the range
- **Lease duration** — typically 8h (workstations) or 24h; shorter for guest networks
- **Option 3** — Default gateway
- **Option 6** — DNS servers
- **Option 15** — DNS domain name
- **Option 51** — Lease time (seconds)

### DHCP Reservations (Static Leases)
Bind a specific MAC address to a fixed IP — the device always receives the same IP while still using DHCP. Preferred over fully static IP for ease of management.

```bash
# Linux (isc-dhcp-server) — /etc/dhcp/dhcpd.conf
host printer-floor2 {
  hardware ethernet 00:1A:2B:3C:4D:5E;
  fixed-address 192.168.1.25;
}
```

### Common DHCP Scope Config (Linux isc-dhcp-server)
```bash
# /etc/dhcp/dhcpd.conf
subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.100 192.168.1.200;
  option routers 192.168.1.1;
  option domain-name-servers 192.168.1.53, 8.8.8.8;
  option domain-name "corp.example.com";
  default-lease-time 28800;    # 8 hours
  max-lease-time 86400;        # 24 hours
}
```

```bash
# Check active leases
cat /var/lib/dhcpd/dhcpd.leases

# Restart service
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server
```

### DHCP Security Risks
- **DHCP starvation** — attacker floods server with DISCOVER requests (spoofed MACs), exhausting the pool. Mitigation: DHCP snooping on managed switches.
- **Rogue DHCP server** — attacker runs own DHCP server, hands out malicious gateway/DNS. Mitigation: DHCP snooping trusts only designated ports.
- **DHCP snooping** — Layer 2 switch feature; marks uplink ports as "trusted" for DHCP replies; blocks DHCP offers from untrusted ports.

---

## VLANs — Virtual Local Area Networks

VLANs logically segment a physical network at Layer 2 (Data Link). Devices on different VLANs cannot communicate directly — traffic must pass through a router or Layer 3 switch. Defined by IEEE 802.1Q.

**Why segment with VLANs:**
- Reduce broadcast domain size (less unnecessary traffic)
- Isolate sensitive systems (HR, finance, IoT, guest)
- Meet compliance requirements (PCI DSS requires cardholder data environment isolation)
- Limit lateral movement if a host is compromised

### VLAN Tagging (IEEE 802.1Q)
802.1Q inserts a 4-byte tag into the Ethernet frame header identifying the VLAN ID (VID, 1–4094). VID 0 is reserved; VID 1 is the default native VLAN on most switches.

```
Normal Ethernet frame:
[ Dst MAC | Src MAC | EtherType | Payload | FCS ]

802.1Q tagged frame:
[ Dst MAC | Src MAC | 0x8100 | Priority + VID | EtherType | Payload | FCS ]
```

### Access Ports vs Trunk Ports

| Port Type | Carries | Used For |
|---|---|---|
| **Access port** | Single VLAN, untagged | End devices (workstations, printers, APs) |
| **Trunk port** | Multiple VLANs, tagged | Switch-to-switch, switch-to-router links |

**Native VLAN** — the VLAN whose traffic is sent untagged on a trunk. Mismatched native VLANs between trunk partners can cause traffic to "jump" VLANs (VLAN hopping risk).

### Switch Configuration (Cisco IOS syntax — [VERIFY: exact command syntax against your IOS version])
```
! Create VLANs
vlan 10
  name SERVERS
vlan 20
  name WORKSTATIONS
vlan 30
  name GUEST

! Set access port (single VLAN)
interface GigabitEthernet0/1
  switchport mode access
  switchport access vlan 10

! Set trunk port (multiple VLANs)
interface GigabitEthernet0/24
  switchport mode trunk
  switchport trunk allowed vlan 10,20,30
  switchport trunk native vlan 999    ! Use unused VLAN as native
```

### Inter-VLAN Routing
Devices on different VLANs require a Layer 3 device to forward traffic between them.

**Option A — Router-on-a-stick:** Single physical link to router, sub-interfaces per VLAN.
**Option B — Layer 3 switch (SVI):** Switch virtual interfaces, faster (hardware-switched).

```
! Layer 3 switch SVIs
interface Vlan10
  ip address 192.168.10.1 255.255.255.0
  no shutdown
interface Vlan20
  ip address 192.168.20.1 255.255.255.0
  no shutdown
ip routing    ! Enable Layer 3 routing
```

### VLAN Security Notes
- **VLAN hopping (double-tagging):** Attacker on native VLAN sends double-tagged frame that gets forwarded to target VLAN. Mitigation: always set native VLAN to an unused VID and tag it explicitly.
- **VLAN is not a security boundary alone** — a misconfigured trunk or a compromised Layer 3 device breaks isolation. Layer 3 firewall rules should still be applied between VLANs.

---

## Firewall Rules

A firewall filters traffic based on defined rules, either stateless (match each packet independently) or stateful (track connection state and allow return traffic automatically).

### Rule Anatomy
Every firewall rule contains:
- **Direction** — inbound or outbound (relative to the interface)
- **Source** — IP address / range / any
- **Destination** — IP address / range / any
- **Protocol** — TCP, UDP, ICMP, or any
- **Port(s)** — source and/or destination port
- **Action** — ACCEPT / DROP / REJECT

**Implicit deny:** Most firewalls end their ruleset with an implicit "deny all." Explicit rules must permit traffic before it reaches the deny.

**Rule order matters:** Rules are evaluated top-to-bottom; first match wins.

### iptables (Linux)

```bash
# View all rules with line numbers
sudo iptables -L -v -n --line-numbers

# Allow established/related connections (stateful)
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow SSH inbound from specific subnet only
sudo iptables -A INPUT -p tcp -s 10.0.0.0/24 --dport 22 -j ACCEPT

# Allow HTTP and HTTPS inbound from anywhere
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Allow ICMP (ping)
sudo iptables -A INPUT -p icmp -j ACCEPT

# Drop all other inbound traffic (explicit deny)
sudo iptables -A INPUT -j DROP

# Block a specific IP
sudo iptables -I INPUT 1 -s 203.0.113.5 -j DROP

# Delete a rule by line number
sudo iptables -D INPUT 3

# Save rules (Debian/Ubuntu)
sudo iptables-save > /etc/iptables/rules.v4
```

**iptables chains:**
- `INPUT` — traffic destined for the local host
- `OUTPUT` — traffic originating from the local host
- `FORWARD` — traffic passing through (routing/NAT scenarios)

### UFW (Uncomplicated Firewall — Ubuntu/Debian)

```bash
# Enable UFW
sudo ufw enable
sudo ufw status verbose

# Allow service by name
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https

# Allow specific port and protocol
sudo ufw allow 8080/tcp

# Allow from specific IP to specific port
sudo ufw allow from 10.0.0.0/24 to any port 22

# Deny inbound (explicit)
sudo ufw deny 23/tcp

# Delete a rule
sudo ufw delete allow 8080/tcp

# Default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

### firewalld (RHEL/CentOS/Fedora)

```bash
# View current zone and rules
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --zone=public --list-all

# Allow a service permanently
sudo firewall-cmd --zone=public --add-service=https --permanent

# Allow a specific port
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent

# Remove a service
sudo firewall-cmd --zone=public --remove-service=telnet --permanent

# Reload to apply permanent rules
sudo firewall-cmd --reload
```

### Firewall Rule Design Patterns
| Scenario | Rule Logic |
|---|---|
| Permit SSH from admin subnet only | Allow TCP/22 from `10.0.0.0/24`; deny TCP/22 from `any` |
| Allow web server | Allow TCP/80, TCP/443 inbound; deny all other inbound |
| Block outbound to known bad IP | Deny all protocols to `203.0.113.0/24` |
| Stateful return traffic | Allow `ESTABLISHED,RELATED` before specific port rules |
| Inter-VLAN — servers to workstations: deny | Allow `192.168.10.0/24` (servers) → `192.168.20.0/24` (WS) only on specific ports; drop rest |

---

## NTP — Network Time Protocol

Defined in RFC 5905. NTP synchronizes clocks across networked hosts. Operates on UDP port 123.

**Why time sync matters for security:**
- Kerberos authentication tolerates only ±5 minutes clock skew by default — a drift beyond that breaks authentication
- Log correlation and forensic timelines are only meaningful if all systems share a common time reference
- Certificate validity and TLS handshakes depend on accurate time
- Intrusion detection and SIEM correlation rules rely on synchronized timestamps

### NTP Hierarchy (Stratum)
```
Stratum 0 — Reference clocks (GPS, atomic clocks, radio signals)
     ↓
Stratum 1 — Servers directly connected to stratum 0 (public NTP servers: time.google.com, pool.ntp.org)
     ↓
Stratum 2 — Servers syncing from stratum 1 (internal NTP servers)
     ↓
Stratum 3+ — Clients and lower-priority servers
```
Lower stratum = more accurate. Most enterprise hosts run at stratum 3–5. Stratum 16 means "unsynchronized."

### NTP Configuration (Linux — chrony, the modern default)

```bash
# Check sync status
chronyc tracking
chronyc sources -v

# /etc/chrony.conf — configure servers
server time.google.com iburst
server 0.pool.ntp.org iburst
server 1.pool.ntp.org iburst
# Allow local subnet clients to query this server
allow 192.168.1.0/24

# Restart chrony
sudo systemctl restart chronyd
sudo systemctl enable chronyd

# Force immediate sync
sudo chronyc makestep
```

### NTP Configuration (Linux — timedatectl / systemd-timesyncd)

```bash
# Check current time sync status
timedatectl status
timedatectl show-timesync

# /etc/systemd/timesyncd.conf
[Time]
NTP=time.google.com 0.pool.ntp.org
FallbackNTP=ntp.ubuntu.com

# Restart
sudo systemctl restart systemd-timesyncd
```

### NTP Security Considerations
- **NTP amplification (DDoS):** Attacker spoofs victim IP in `monlist` queries — small request, large response. Mitigation: disable `monlist` (disabled by default in NTP 4.2.7+), restrict query sources.
- **NTP spoofing / time attack:** Attacker shifts a host's clock to break Kerberos, expire certificates, or manipulate logs. Mitigation: use authenticated NTP (NTPsec or chrony with symmetric keys), use multiple stratum-1 sources.
- **Stranded client (stratum 16):** Host has no valid time source. Kerberos will fail. Check firewall rules — UDP 123 must be permitted outbound (or to internal NTP server).

```bash
# Verify NTP port is reachable (Linux)
nc -zu time.google.com 123 && echo "NTP reachable"

# Windows — sync time immediately
w32tm /resync /force
w32tm /query /status
```

---

## Common Attack Vectors / Misconfigurations

| Protocol / Concept | Attack | Mitigation |
|---|---|---|
| ARP | ARP spoofing / poisoning → MITM | Dynamic ARP Inspection (DAI) on managed switches |
| DNS | Cache poisoning, tunneling, hijacking, AXFR leak | DNSSEC, restrict AXFR, monitor DNS query volume |
| DHCP | Starvation, rogue DHCP server | DHCP snooping, port security on switches |
| VLAN | Double-tagging / VLAN hopping | Unused native VLAN, disable DTP, explicit tagging |
| NTP | Amplification, clock skew attack | Authenticated NTP, disable monlist, restrict queries |
| Firewall | Permit-any rules, implicit allow, no egress filtering | Default deny, restrict egress, log denied traffic |
| Unencrypted protocols | Sniffing HTTP, FTP, Telnet | Enforce TLS; disable legacy protocols |
| Open ports | Unnecessary exposed services | Port scan regularly; close/firewall unused ports |
| SMB | Lateral movement, EternalBlue | Block 445 at perimeter; patch; use SMB signing |

---

## Common Scenarios

**Scenario 1 — New host can't reach the network**
1. Check if IP assigned: `ip addr` / `ipconfig`
2. APIPA address (`169.254.x.x`) → DHCP failure. Check server, scope exhaustion, DHCP snooping trust.
3. Ping default gateway. Fail → check VLAN assignment on switch port.
4. Ping DNS server. Fail → firewall rule or routing issue.
5. Resolve a name: `dig example.com @8.8.8.8`. Fail → DNS misconfiguration.

**Scenario 2 — Kerberos authentication failing**
1. Check clock skew: `chronyc tracking` or `w32tm /query /status`
2. If drift > 5 min → force sync: `chronyc makestep` / `w32tm /resync`
3. Verify UDP 123 is permitted to NTP server in firewall rules.

**Scenario 3 — DNS zone change not propagating**
1. Check SOA serial — secondary won't pull if primary serial ≤ secondary serial.
2. Increment serial (use YYYYMMDDnn format), save zone file, reload nameserver.
3. Check refresh interval — secondary won't check until refresh timer elapses (default often 1h).
4. Force zone reload on BIND: `rndc reload example.com`

**Scenario 4 — Two VLANs can't communicate**
1. Confirm hosts are on different VLANs: check switch port VLAN assignment.
2. Confirm Layer 3 routing exists between VLANs: SVIs configured, `ip routing` enabled.
3. Check firewall rules between VLAN subnets — an ACL on the SVI may be blocking.
4. Confirm default gateways on end hosts point to the correct SVI IP.

---

## Related Notes
- OSI-Model
- TCP-IP-Fundamentals
- Common-Ports
- Linux-Commands
- Windows-Commands
- Wireshark
- Nmap
- Network-Defense-Blue-Team

## Sources

Internet Engineering Task Force. (1981). *Internet protocol* (RFC 791). IETF. https://www.rfc-editor.org/rfc/rfc791

Internet Engineering Task Force. (1981). *Transmission control protocol* (RFC 793). IETF. https://www.rfc-editor.org/rfc/rfc793

Internet Engineering Task Force. (1980). *User datagram protocol* (RFC 768). IETF. https://www.rfc-editor.org/rfc/rfc768

Internet Engineering Task Force. (1987). *Domain names — concepts and facilities* (RFC 1034). IETF. https://www.rfc-editor.org/rfc/rfc1034

Internet Engineering Task Force. (1996). *Address allocation for private internets* (RFC 1918). IETF. https://www.rfc-editor.org/rfc/rfc1918

Internet Engineering Task Force. (1997). *Dynamic host configuration protocol* (RFC 2131). IETF. https://www.rfc-editor.org/rfc/rfc2131

Internet Engineering Task Force. (2011). *Network time protocol version 4: Protocol and algorithms specification* (RFC 5905). IETF. https://www.rfc-editor.org/rfc/rfc5905

Institute of Electrical and Electronics Engineers. (2022). *IEEE standard for local and metropolitan area network — bridges and bridged networks* (IEEE 802.1Q). IEEE.

Internet Systems Consortium. (2024). *ISC DHCP server documentation*. ISC. https://www.isc.org/dhcp/

CompTIA. (2024). *CompTIA network+ study guide*. CompTIA.

---
<- [[Fundamentals-MOC]]