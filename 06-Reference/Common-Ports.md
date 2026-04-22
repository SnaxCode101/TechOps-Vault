---
title: Common Ports Reference
tags:
  - reference
  - cheat-sheet
  - networking
  - ports
  - foundational
created: 2026-03-23
updated: 2026-04-04
difficulty: foundational
---

# Common Ports Reference

Quick lookup for ports encountered during scanning, CTFs, and penetration tests.

---

## Well-Known Ports (0–1023)

| Port | Protocol | Service | Notes |
|---|---|---|---|
| 20 | TCP | FTP Data | Unencrypted file transfer data channel |
| 21 | TCP | FTP Control | Unencrypted; credentials sent in plaintext |
| 22 | TCP | SSH | Secure shell; also used by SFTP and SCP |
| 23 | TCP | Telnet | Unencrypted remote shell; legacy; avoid |
| 25 | TCP | SMTP | Email sending; often filtered outbound |
| 53 | TCP/UDP | DNS | Domain resolution; UDP standard, TCP for zone transfers |
| 67 | UDP | DHCP Server | Server listens here |
| 68 | UDP | DHCP Client | Client listens here |
| 69 | UDP | TFTP | Trivial FTP; no auth; used in PXE boot |
| 80 | TCP | HTTP | Unencrypted web traffic |
| 88 | TCP/UDP | Kerberos | Authentication; key AD port |
| 110 | TCP | POP3 | Email retrieval (unencrypted) |
| 111 | TCP/UDP | RPC / portmapper | Remote Procedure Call |
| 119 | TCP | NNTP | Usenet news; rarely seen now |
| 123 | UDP | NTP | Time synchronization |
| 135 | TCP | MS-RPC / DCOM | Windows RPC endpoint mapper |
| 137-139 | TCP/UDP | NetBIOS | Windows file/printer sharing (legacy) |
| 143 | TCP | IMAP | Email retrieval (unencrypted) |
| 161 | UDP | SNMP | Network device management; v1/v2c use community strings |
| 162 | UDP | SNMP Trap | Device sends alerts to manager |
| 389 | TCP/UDP | LDAP | Directory services; Active Directory |
| 443 | TCP | HTTPS | TLS-encrypted web traffic |
| 445 | TCP | SMB | Windows file sharing; EternalBlue target |
| 465 | TCP | SMTPS | SMTP over SSL (legacy) |
| 500 | UDP | IKE / IPSec | VPN key exchange |
| 514 | UDP | Syslog | Log forwarding |
| 587 | TCP | SMTP (Submission) | Authenticated email sending |
| 631 | TCP | IPP | Internet Printing Protocol |
| 636 | TCP | LDAPS | LDAP over SSL |
| 993 | TCP | IMAPS | IMAP over SSL |
| 995 | TCP | POP3S | POP3 over SSL |

---

## Registered Ports (1024–49151)

| Port | Protocol | Service | Notes |
|---|---|---|---|
| 1080 | TCP | SOCKS Proxy | Often used for tunneling |
| 1194 | UDP | OpenVPN | Common VPN port |
| 1433 | TCP | MS SQL Server | Microsoft SQL database |
| 1521 | TCP | Oracle DB | Oracle database listener |
| 2049 | TCP/UDP | NFS | Network File System |
| 2375 | TCP | Docker | Unencrypted Docker API — critical exposure |
| 2376 | TCP | Docker TLS | TLS-encrypted Docker API |
| 3306 | TCP | MySQL / MariaDB | Common database; check for external exposure |
| 3389 | TCP | RDP | Windows Remote Desktop Protocol |
| 4444 | TCP | Metasploit default | Default MSF reverse shell listener |
| 5432 | TCP | PostgreSQL | Common database |
| 5900 | TCP | VNC | Remote desktop (often weak/no auth) |
| 5985 | TCP | WinRM (HTTP) | Windows Remote Management |
| 5986 | TCP | WinRM (HTTPS) | Windows Remote Management over TLS |
| 6379 | TCP | Redis | In-memory database; often no auth by default |
| 8080 | TCP | HTTP-alt / Proxy | Common dev server port; Burp Suite default |
| 8443 | TCP | HTTPS-alt | Alternate HTTPS |
| 8888 | TCP | Jupyter Notebook | Often exposed without auth in dev environments |
| 9200 | TCP | Elasticsearch | Often exposed without auth; critical data risk |
| 27017 | TCP | MongoDB | Default port; often exposed without auth |

---

## Security-Relevant Port Patterns

### "See these → investigate immediately"
| Port(s) | Why |
|---|---|
| 23 (Telnet) | Plaintext credentials |
| 2375 (Docker unencrypted) | Remote code execution as root |
| 6379 (Redis), 9200 (Elastic), 27017 (Mongo) | Databases often with no auth |
| 5900 (VNC) | Weak or no auth remote desktop |
| 161 (SNMP v1/v2c) | Default community strings = full device info |
| 21 (FTP) | Anonymous login may be enabled |
| 445 (SMB) | Check for EternalBlue (MS17-010) and null sessions |

### Active Directory Key Ports
`88` (Kerberos) · `389/636` (LDAP/LDAPS) · `445` (SMB) · `135` (RPC) · `3268/3269` (Global Catalog) · `5985/5986` (WinRM)

---

## Related Notes
- Nmap
- Networking-Basics
- TCP-IP-Fundamentals

## See Also

- Nmap
- Networking-Basics
- TCP-IP-Fundamentals
- Penetration-Testing-Methodology

---

## Additional Registered Ports

| Port | Protocol | Service | Notes |
|---|---|---|---|
| 3000 | TCP | Grafana / Node.js dev | Grafana default; also common for dev web servers |
| 4369 | TCP | EPMD (Erlang) | RabbitMQ node discovery |
| 5000 | TCP | Flask / dev servers | Python Flask default; Docker Registry |
| 5601 | TCP | Kibana | Elasticsearch dashboard — often no auth in dev |
| 5672 | TCP | AMQP | RabbitMQ message broker |
| 6443 | TCP | Kubernetes API | K8s API server — critical exposure if public |
| 8161 | TCP | ActiveMQ web console | Default admin console — often default creds |
| 8888 | TCP | Jupyter Notebook | Often no auth in dev/data science environments |
| 9090 | TCP | Prometheus | Metrics server — may expose system internals |
| 9200 | TCP | Elasticsearch HTTP | REST API — frequently exposed without auth |
| 9300 | TCP | Elasticsearch cluster | Node-to-node communication |
| 10250 | TCP | Kubelet API | K8s node agent — unauthenticated = RCE risk |
| 15672 | TCP | RabbitMQ mgmt UI | Web console — check for default creds |
| 27017 | TCP | MongoDB | Default port; often no auth in dev |
| 50000 | TCP | Jenkins agent | Build agent port |
| 50070 | TCP | Hadoop NameNode | HDFS admin UI |

---

## Ports by Category (Quick Lookup)

**Remote Access**
`22` SSH · `23` Telnet · `3389` RDP · `5900` VNC · `5985/5986` WinRM

**Web**
`80` HTTP · `443` HTTPS · `8080` HTTP-alt · `8443` HTTPS-alt · `3000` dev · `5000` dev · `8888` Jupyter

**Email**
`25` SMTP · `465` SMTPS · `587` Submission · `110` POP3 · `995` POP3S · `143` IMAP · `993` IMAPS

**DNS / Directory / Auth**
`53` DNS · `88` Kerberos · `389` LDAP · `636` LDAPS · `3268` Global Catalog · `3269` GC SSL

**File Sharing / Transfer**
`20/21` FTP · `22` SFTP/SCP · `69` TFTP · `139/445` SMB · `2049` NFS · `873` rsync

**Databases**
`1433` MSSQL · `1521` Oracle · `3306` MySQL/MariaDB · `5432` PostgreSQL · `6379` Redis · `9200` Elasticsearch · `27017` MongoDB

**Monitoring / Observability**
`161/162` SNMP · `9090` Prometheus · `3000` Grafana · `5601` Kibana · `514` Syslog

**Message Queues**
`5672` AMQP/RabbitMQ · `9092` Kafka · `61616` ActiveMQ

**Container / Cloud-Native**
`2375` Docker (unencrypted) · `2376` Docker (TLS) · `6443` Kubernetes API · `10250` Kubelet · `2379/2380` etcd

**VPN / Tunneling**
`500` IKE/IPSec · `1194` OpenVPN · `1723` PPTP · `4500` IPSec NAT-T · `51820` WireGuard

---
<- [[Reference-MOC]]