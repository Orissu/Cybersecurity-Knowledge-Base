# 🛠️ Snort

> **Category:** Network Intrusion Detection/Prevention System (NIDS/NIPS)
> **When to Use:** Monitoring network traffic for malicious activity, writing detection rules.

---

## 📋 Operating Modes

| Mode | Command | Purpose |
|------|---------|---------|
| Sniffer | `snort -v` | Display packets in real-time |
| Packet Logger | `snort -dev -l /var/log/snort` | Log packets to disk |
| NIDS | `snort -c /etc/snort/snort.conf -A console` | Alert on rule matches |
| Inline/IPS | `snort -Q --daq afpacket` | Block malicious traffic |

---

## 📜 Rule Structure

```
[Action] [Protocol] [Source IP] [Source Port] -> [Dest IP] [Dest Port] (Options)
```

### Example
```
alert tcp $EXTERNAL_NET any -> $HOME_NET 80 (msg:"SQL Injection Attempt"; content:"UNION SELECT"; nocase; sid:1000001; rev:1;)
```

### Actions
| Action | Description |
|--------|-------------|
| `alert` | Generate alert and log packet |
| `log` | Log packet only |
| `pass` | Ignore packet |
| `drop` | Block and log (IPS mode) |
| `reject` | Block, log, and send RST/ICMP unreachable |

### Protocols
`tcp`, `udp`, `icmp`, `ip`

### Direction Operators
| Operator | Meaning |
|----------|---------|
| `->` | Source to destination |
| `<>` | Bidirectional |

---

## 🔧 Rule Options (Most Important)

### Content Matching
| Option | Description | Example |
|--------|-------------|---------|
| `content:"string"` | Match exact string | `content:"admin"` |
| `nocase` | Case-insensitive match | `content:"select"; nocase;` |
| `content:\|hex\|` | Match hex bytes | `content:\|90 90 90\|;` |
| `offset:<n>` | Start searching at byte N | `offset:4;` |
| `depth:<n>` | Search only first N bytes | `depth:20;` |
| `distance:<n>` | Bytes between content matches | `distance:0;` |
| `within:<n>` | Max bytes for next match | `within:10;` |

### Flow Control
| Option | Description |
|--------|-------------|
| `flow:to_server,established` | Client → Server on established connection |
| `flow:to_client,established` | Server → Client response |
| `flow:stateless` | Don't track connection state |

### Detection
| Option | Description |
|--------|-------------|
| `pcre:"/regex/"` | Perl-compatible regex |
| `byte_test` | Compare byte values |
| `threshold` | Rate-based detection |
| `flowbits` | Track state across packets |

### Metadata
| Option | Description |
|--------|-------------|
| `msg:"description"` | Alert message |
| `sid:<number>` | Unique rule ID (1000000+ for custom) |
| `rev:<number>` | Rule revision |
| `classtype:<type>` | Attack classification |
| `priority:<1-4>` | Alert priority (1=highest) |
| `reference:cve,2021-44228` | CVE reference |

---

## 💡 Custom Rule Examples

```bash
# Detect reverse shell (outbound bash)
alert tcp $HOME_NET any -> $EXTERNAL_NET any (msg:"Possible Reverse Shell"; content:"/bin/bash"; sid:1000002; rev:1;)

# Detect Nmap SYN scan
alert tcp $EXTERNAL_NET any -> $HOME_NET any (msg:"Nmap SYN Scan"; flags:S; threshold:type both, track by_src, count 20, seconds 5; sid:1000003; rev:1;)

# Detect SQL injection in HTTP
alert tcp $EXTERNAL_NET any -> $HOME_NET 80 (msg:"SQLi Attempt"; flow:to_server,established; content:"UNION"; nocase; content:"SELECT"; nocase; distance:0; within:20; sid:1000004; rev:1;)

# Detect Log4Shell (CVE-2021-44228)
alert tcp any any -> $HOME_NET any (msg:"Log4Shell Attempt"; content:"${jndi:"; nocase; sid:1000005; rev:1;)

# Detect ICMP tunnel (large ICMP packets)
alert icmp any any -> $HOME_NET any (msg:"Possible ICMP Tunnel"; dsize:>800; sid:1000006; rev:1;)

# Detect PowerShell download cradle
alert tcp $HOME_NET any -> $EXTERNAL_NET any (msg:"PowerShell Download"; content:"IEX"; nocase; content:"Net.WebClient"; nocase; sid:1000007; rev:1;)
```

---

## ⚙️ Configuration

### Key Variables (snort.conf)
```bash
var HOME_NET 192.168.1.0/24          # Your network
var EXTERNAL_NET !$HOME_NET           # Everything else
var HTTP_SERVERS $HOME_NET            # Web servers
var DNS_SERVERS 192.168.1.1           # DNS servers
```

### Alert Output Modes
| Flag | Output |
|------|--------|
| `-A console` | Print to terminal |
| `-A fast` | One-line alerts to file |
| `-A full` | Full packet headers |
| `-A unsock` | Unix socket (for SIEM) |

---

## 💡 Expert Tips

| Tip | Details |
|-----|---------|
| Use `sid` 1000000+ | Avoids conflicts with official rules |
| Rule order matters | `drop` rules before `alert` in IPS mode |
| Test rules first | `snort -T -c snort.conf` validates config |
| Tune for FPs | Disable noisy rules, adjust thresholds |
| Use `flowbits` | Track multi-packet attacks (sessions) |

---

## 🔗 Links

- Combine with: Splunk/ELK (send alerts to SIEM)
- Alternative: Suricata (multi-threaded, faster, compatible rules)
- Related: Sigma rules (generic detection format), YARA (file-based)
- Practice: THM Snort room, Security Onion
