# 🛠️ TCPdump

> **Category:** Command-Line Packet Analyzer
> **When to Use:** Capturing network traffic, forensic analysis, debugging connections, remote sniffing.

---

## 📋 Basic Syntax

```bash
tcpdump [options] [filter expression]
```

---

## 🎯 Essential Capture Commands

| Command | Description |
|---------|-------------|
| `tcpdump -i eth0` | Capture on specific interface |
| `tcpdump -i any` | Capture on all interfaces |
| `tcpdump -w capture.pcap` | Write to PCAP file |
| `tcpdump -r capture.pcap` | Read from PCAP file |
| `tcpdump -c 100` | Stop after 100 packets |
| `tcpdump -D` | List available interfaces |

---

## 🔍 Display & Formatting Flags

| Flag | Description | Use Case |
|------|-------------|----------|
| `-n` | No DNS resolution (show IPs) | Faster, no DNS leaks |
| `-nn` | No DNS + no port names | Show raw port numbers |
| `-v` | Verbose (TTL, ID, length) | More packet details |
| `-vv` | Very verbose | Even more details |
| `-vvv` | Maximum verbosity | Full packet decode |
| `-A` | ASCII payload | Read HTTP, FTP, SMTP content |
| `-X` | Hex + ASCII | Binary protocol analysis |
| `-xx` | Hex including link-layer | Full raw packet |
| `-e` | Show MAC addresses | Layer 2 analysis |
| `-q` | Quiet (less output) | Quick overview |
| `-t` | No timestamp | Cleaner output |
| `-tttt` | Full date timestamp | Forensic timeline |
| `-S` | Absolute sequence numbers | TCP analysis |

---

## 🛠️ BPF Filters (Berkeley Packet Filter)

### Host Filters
```bash
tcpdump host 192.168.1.10              # Traffic to/from IP
tcpdump src host 192.168.1.10          # Only from this IP
tcpdump dst host 192.168.1.10          # Only to this IP
tcpdump net 192.168.1.0/24             # Entire subnet
```

### Port Filters
```bash
tcpdump port 80                        # HTTP traffic
tcpdump port 443                       # HTTPS traffic
tcpdump src port 53                    # DNS responses
tcpdump dst port 22                    # SSH connections
tcpdump portrange 1-1024              # Port range
```

### Protocol Filters
```bash
tcpdump tcp                            # TCP only
tcpdump udp                            # UDP only
tcpdump icmp                           # ICMP only
tcpdump arp                            # ARP only
```

### Combining Filters
```bash
# AND
tcpdump host 192.168.1.10 and port 80

# OR
tcpdump port 80 or port 443

# NOT
tcpdump not port 22

# Complex
tcpdump 'src 192.168.1.10 and (dst port 80 or dst port 443)'
```

### TCP Flag Filters
```bash
# SYN packets only (connection attempts)
tcpdump 'tcp[tcpflags] == tcp-syn'

# SYN-ACK packets
tcpdump 'tcp[tcpflags] == tcp-syn|tcp-ack'

# RST packets (connection resets)
tcpdump 'tcp[tcpflags] & tcp-rst != 0'

# FIN packets (connection close)
tcpdump 'tcp[tcpflags] & tcp-fin != 0'

# PSH+ACK (data transfer)
tcpdump 'tcp[tcpflags] & (tcp-push|tcp-ack) != 0'
```

### Size Filters
```bash
tcpdump greater 500                    # Packets > 500 bytes
tcpdump less 100                       # Packets < 100 bytes
```

---

## 💡 Expert One-Liners

```bash
# === CREDENTIAL SNIFFING ===

# Capture HTTP credentials (unencrypted)
tcpdump -i eth0 -A -s0 'tcp port 80 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'

# Capture FTP credentials
tcpdump -i eth0 -nn -A port 21 | grep -i "user\|pass"

# Capture SMTP credentials
tcpdump -i eth0 -nn -A port 25 | grep -i "auth\|user\|pass"

# === NETWORK ANALYSIS ===

# Find all DNS queries
tcpdump -i eth0 -nn port 53

# Capture only HTTP GET requests
tcpdump -i eth0 -A -s0 'tcp port 80 and tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420'

# Capture HTTP POST requests
tcpdump -i eth0 -A -s0 'tcp port 80 and tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x504F5354'

# Find SYN flood (many SYN without ACK)
tcpdump -i eth0 'tcp[tcpflags] == tcp-syn' -c 1000

# === FORENSICS ===

# Full capture with timestamps for forensics
tcpdump -i eth0 -nn -tttt -w /tmp/forensic_$(date +%Y%m%d).pcap

# Capture and rotate files (100MB each, keep 10)
tcpdump -i eth0 -w /tmp/capture -C 100 -W 10

# Read pcap and filter
tcpdump -r capture.pcap 'host 10.10.10.10 and port 4444'

# === QUICK CHECKS ===

# Who is talking on the network?
tcpdump -i eth0 -nn -c 100 | awk '{print $3}' | cut -d. -f1-4 | sort -u

# Check for ARP spoofing
tcpdump -i eth0 arp

# Detect port scanning
tcpdump -i eth0 'tcp[tcpflags] == tcp-syn' -nn | awk '{print $5}' | sort | uniq -c | sort -rn
```

---

## ⚠️ Important Notes

| Note | Details |
|------|---------|
| Requires root | Use `sudo tcpdump` |
| `-s0` for full packets | Default may truncate (use `-s 0` or `-s0`) |
| Write to file for Wireshark | `-w file.pcap` then open in Wireshark |
| Performance | On busy networks, use filters to reduce load |
| Legal | Only capture on networks you're authorized to monitor |

---

## 🔗 Links

- After capture → open .pcap in Wireshark for GUI analysis
- Combine with: grep, awk, sort for quick CLI analysis
- Alternative: tshark (Wireshark CLI), Wireshark (GUI)
- Related: Snort (alerting on patterns), Zeek (network metadata)
