# 🌐 Network Architecture & Protocols

> **Scope:** TCP/IP fundamentals, protocols, and network concepts essential for pentesting.

---

## 📋 OSI Model (7 Layers)

| Layer | Name | Protocols | Pentest Relevance |
|-------|------|-----------|-------------------|
| 7 | Application | HTTP, FTP, SSH, DNS, SMTP | Web attacks, service exploitation |
| 6 | Presentation | SSL/TLS, JPEG, ASCII | SSL stripping, encoding attacks |
| 5 | Session | NetBIOS, RPC | Session hijacking |
| 4 | Transport | TCP, UDP | Port scanning, SYN floods |
| 3 | Network | IP, ICMP, ARP | IP spoofing, ARP poisoning |
| 2 | Data Link | Ethernet, WiFi | MAC spoofing, VLAN hopping |
| 1 | Physical | Cables, signals | Physical access attacks |

---

## 🔌 TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Connection-oriented (3-way handshake) | Connectionless |
| Reliability | Guaranteed delivery (ACK) | Best-effort (no ACK) |
| Speed | Slower (overhead) | Faster (no overhead) |
| Use cases | HTTP, SSH, FTP, SMB | DNS, DHCP, SNMP, VoIP |
| Scanning | SYN scan (-sS), Connect scan (-sT) | UDP scan (-sU) |

### TCP 3-Way Handshake
```
Client → SYN → Server          (I want to connect)
Client ← SYN-ACK ← Server     (OK, I acknowledge)
Client → ACK → Server          (Connection established)
```

### TCP Flags (for packet analysis)
| Flag | Purpose | Pentest Use |
|------|---------|-------------|
| SYN | Initiate connection | Port scanning |
| ACK | Acknowledge | Firewall mapping |
| FIN | Close connection | Stealth scanning |
| RST | Reset/abort | Port closed indicator |
| PSH | Push data immediately | Data transfer |
| URG | Urgent data | Rarely used |

---

## 📡 Key Protocols & Ports

### Must-Know Ports
| Port | Protocol | Service | Attack Surface |
|------|----------|---------|---------------|
| 21 | TCP | FTP | Anonymous login, brute-force |
| 22 | TCP | SSH | Brute-force, key theft |
| 23 | TCP | Telnet | Cleartext credentials |
| 25 | TCP | SMTP | Email spoofing, relay |
| 53 | TCP/UDP | DNS | Zone transfer, tunneling |
| 80 | TCP | HTTP | Web attacks (SQLi, XSS) |
| 88 | TCP | Kerberos | Kerberoasting, AS-REP |
| 110 | TCP | POP3 | Credential sniffing |
| 111 | TCP | RPCbind | NFS enumeration |
| 135 | TCP | MSRPC | Windows RPC attacks |
| 139 | TCP | NetBIOS | Legacy SMB attacks |
| 143 | TCP | IMAP | Credential sniffing |
| 161 | UDP | SNMP | Community string brute-force |
| 389 | TCP | LDAP | AD enumeration |
| 443 | TCP | HTTPS | Web attacks (encrypted) |
| 445 | TCP | SMB | EternalBlue, relay, shares |
| 464 | TCP | Kerberos passwd | Password changes |
| 593 | TCP | HTTP RPC | Windows RPC over HTTP |
| 636 | TCP | LDAPS | Encrypted LDAP |
| 1433 | TCP | MSSQL | SQL injection, xp_cmdshell |
| 1521 | TCP | Oracle | DB attacks |
| 2049 | TCP | NFS | no_root_squash exploit |
| 3306 | TCP | MySQL | SQL injection, UDF |
| 3389 | TCP | RDP | Brute-force, BlueKeep |
| 5432 | TCP | PostgreSQL | SQL injection |
| 5985 | TCP | WinRM | Lateral movement |
| 5986 | TCP | WinRM (HTTPS) | Lateral movement |
| 6379 | TCP | Redis | Unauthenticated access |
| 8080 | TCP | HTTP Proxy | Web attacks |
| 8443 | TCP | HTTPS Alt | Web attacks |
| 9200 | TCP | Elasticsearch | Unauthenticated access |

---

## 🌐 DNS (Domain Name System)

### Record Types
| Type | Purpose | Example |
|------|---------|---------|
| A | IPv4 address | target.com → 1.2.3.4 |
| AAAA | IPv6 address | target.com → ::1 |
| CNAME | Alias | www.target.com → target.com |
| MX | Mail server | target.com → mail.target.com |
| NS | Name server | target.com → ns1.target.com |
| TXT | Text records | SPF, DKIM, verification |
| PTR | Reverse lookup | 1.2.3.4 → target.com |
| SOA | Start of Authority | Zone info |

### DNS Commands
```bash
# Basic lookup
nslookup target.com
dig target.com
host target.com

# Specific record types
dig target.com MX
dig target.com NS
dig target.com TXT

# Zone transfer (misconfiguration)
dig axfr target.com @ns1.target.com

# Reverse lookup
dig -x 10.10.10.5
```

---

## 🔀 Subnetting Quick Reference

### CIDR Notation
| CIDR | Subnet Mask | Hosts | Use |
|------|-------------|-------|-----|
| /8 | 255.0.0.0 | 16,777,214 | Class A |
| /16 | 255.255.0.0 | 65,534 | Class B |
| /24 | 255.255.255.0 | 254 | Class C (most common) |
| /25 | 255.255.255.128 | 126 | Half a /24 |
| /26 | 255.255.255.192 | 62 | Quarter |
| /27 | 255.255.255.224 | 30 | Small subnet |
| /28 | 255.255.255.240 | 14 | Very small |
| /30 | 255.255.255.252 | 2 | Point-to-point |
| /32 | 255.255.255.255 | 1 | Single host |

### Private IP Ranges
| Range | CIDR | Class |
|-------|------|-------|
| 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 | A |
| 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 | B |
| 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 | C |

---

## 🔒 Common Network Security Concepts

| Concept | Description |
|---------|-------------|
| NAT | Translates private IPs to public (hides internal network) |
| DMZ | Demilitarized zone (public-facing servers isolated) |
| VLAN | Virtual LAN (logical network segmentation) |
| VPN | Encrypted tunnel over public network |
| Proxy | Intermediary for requests (forward/reverse) |
| Load Balancer | Distributes traffic across servers |
| WAF | Web Application Firewall (Layer 7 filtering) |

---

## 🔗 Associated Tools
- [Nmap](../../TOOLS/nmap.md) - Port scanning and service detection
- [TCPdump](../../TOOLS/tcpdump.md) - Packet capture
