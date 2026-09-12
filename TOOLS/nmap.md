# 🛠️ Nmap (Network Mapper)

> **Category:** Network Discovery & Security Auditing
> **When to Use:** First tool in ANY engagement. Maps the attack surface before exploitation.

---

## 📋 Target Specification

| Syntax | Description |
|--------|-------------|
| `nmap 192.168.1.1` | Single IP |
| `nmap target.com` | Hostname |
| `nmap 192.168.1.1-50` | IP range |
| `nmap 192.168.1.0/24` | Subnet (CIDR) |
| `nmap -iL targets.txt` | From file |
| `nmap 192.168.1.0/24 --exclude 192.168.1.1` | Exclude hosts |

---

## 🔍 Scan Types

| Flag | Name | Description | Use Case |
|------|------|-------------|----------|
| `-sS` | TCP SYN (Stealth) | Half-open scan, doesn't complete handshake | Default, fast, stealthy |
| `-sT` | TCP Connect | Full TCP handshake | When you don't have raw socket privileges |
| `-sU` | UDP Scan | Targets UDP services | DNS (53), SNMP (161), DHCP (67) |
| `-sA` | ACK Scan | Determines firewall rules | Mapping firewall rulesets |
| `-sW` | Window Scan | Like ACK but checks window size | Detecting open ports behind firewalls |
| `-sN` | NULL Scan | No flags set | Firewall evasion (RFC compliant) |
| `-sF` | FIN Scan | FIN flag only | Firewall evasion |
| `-sX` | Xmas Scan | FIN+PSH+URG flags | Firewall evasion |

---

## 📡 Host Discovery

| Flag | Description | Use Case |
|------|-------------|----------|
| `-sn` | Ping scan (no port scan) | Map live hosts on a network |
| `-Pn` | Skip host discovery | Target seems down but is alive (firewall blocks ICMP) |
| `-PS <ports>` | TCP SYN ping | Discover hosts via specific ports |
| `-PA <ports>` | TCP ACK ping | Alternative discovery method |
| `-PU <ports>` | UDP ping | Discover hosts via UDP |
| `-PE` | ICMP echo ping | Standard ping |
| `-n` | No DNS resolution | Faster scans |
| `-R` | Always resolve DNS | Get hostnames |

---

## 🎯 Port Specification

| Flag | Description |
|------|-------------|
| `-p 80` | Single port |
| `-p 80,443,8080` | Multiple ports |
| `-p 1-1000` | Port range |
| `-p-` | All 65535 ports |
| `--top-ports 100` | Top 100 most common ports |
| `-p U:53,T:80` | Specific protocol ports |

---

## 🔍 Service & OS Detection

| Flag | Description | Use Case |
|------|-------------|----------|
| `-sV` | Service version detection | Identify software and version on open ports |
| `--version-intensity <0-9>` | Version scan intensity | Higher = more accurate but slower |
| `-O` | OS detection | Identify target operating system |
| `-A` | Aggressive scan | Combines -sV, -O, traceroute, default scripts |
| `--osscan-guess` | Guess OS more aggressively | When OS detection is uncertain |

---

## ⚡ Timing & Performance

| Flag | Description | Use Case |
|------|-------------|----------|
| `-T0` | Paranoid | IDS evasion (very slow) |
| `-T1` | Sneaky | IDS evasion |
| `-T2` | Polite | Reduced bandwidth usage |
| `-T3` | Normal | Default |
| `-T4` | Aggressive | Fast, reliable networks |
| `-T5` | Insane | Very fast, may miss ports |
| `--min-rate <n>` | Minimum packets/sec | Force speed (e.g., 5000) |
| `--max-rate <n>` | Maximum packets/sec | Throttle to avoid detection |
| `--max-retries <n>` | Max probe retries | Lower = faster (default 10) |
| `--host-timeout <time>` | Give up on slow hosts | Skip unresponsive targets |

---

## 📜 Nmap Scripting Engine (NSE)

| Flag | Description |
|------|-------------|
| `-sC` | Run default scripts |
| `--script=<name>` | Run specific script |
| `--script=<category>` | Run category (vuln, exploit, auth, brute, discovery) |
| `--script-args=<args>` | Pass arguments to scripts |
| `--script-help=<name>` | Show script documentation |

### Useful Script Categories
| Category | Purpose | Example |
|----------|---------|---------|
| `vuln` | Vulnerability detection | `--script vuln` |
| `auth` | Authentication checks | `--script auth` |
| `brute` | Brute force attacks | `--script brute` |
| `exploit` | Active exploitation | `--script exploit` |
| `discovery` | Service discovery | `--script discovery` |

### Must-Know Scripts
```bash
# SMB enumeration
nmap --script smb-enum-shares,smb-enum-users -p 445 <target>

# SMB vulnerabilities (EternalBlue)
nmap --script smb-vuln* -p 445 <target>

# HTTP enumeration
nmap --script http-enum,http-headers,http-methods -p 80,443 <target>

# DNS zone transfer
nmap --script dns-zone-transfer -p 53 <target>

# FTP anonymous login
nmap --script ftp-anon -p 21 <target>

# MySQL enumeration
nmap --script mysql-info,mysql-enum -p 3306 <target>

# LDAP enumeration
nmap --script ldap-search -p 389 <target>

# SSL/TLS vulnerabilities
nmap --script ssl-heartbleed,ssl-poodle,ssl-enum-ciphers -p 443 <target>
```

---

## 🎭 Firewall / IDS Evasion

| Flag | Description | Use Case |
|------|-------------|----------|
| `-f` | Fragment packets | Bypass simple packet filters |
| `--mtu <value>` | Custom MTU (multiple of 8) | Evade signature detection |
| `-D RND:5` | 5 random decoy IPs | Hide your real IP in noise |
| `-D <ip1>,<ip2>,ME` | Specific decoys | Blend with known IPs |
| `--source-port 53` | Spoof source port | Trick firewalls (DNS=53, Kerberos=88) |
| `--data-length <n>` | Append random data | Change packet signature |
| `--randomize-hosts` | Random target order | Avoid sequential detection |
| `--spoof-mac <mac>` | Spoof MAC address | Bypass MAC filtering |
| `--badsum` | Send bad checksums | Test firewall/IDS response |

---

## 💾 Output Formats

| Flag | Format | Use Case |
|------|--------|----------|
| `-oN file.txt` | Normal | Saves the output to a file, human-readable |
| `-oG file.txt` | Grepable | Easy to parse with grep/awk |
| `-oX file.xml` | XML | Import into other tools |
| `-oA basename` | All formats | Creates .nmap, .gnmap, .xml |

---

## 💡 Expert One-Liners (Copy-Paste Ready)

```bash
# === INITIAL RECON ===

# Quick host discovery on a subnet
nmap -sn 192.168.1.0/24 -oG - | grep "Up" | awk '{print $2}'

# Fast top-port scan
nmap -T4 --top-ports 1000 -sV -oA initial <target>

# Full port scan (find everything)
nmap -T4 -p- -n -v --min-rate 5000 <target>

# === SERVICE ENUMERATION ===

# Detailed scan on discovered ports
nmap -sC -sV -p 22,80,443,445,3389 -oA detailed <target>

# Aggressive full scan
nmap -A -T4 -p- <target>

# === VULNERABILITY SCANNING ===

# Full vuln scan
nmap -Pn -sV --script vuln -oA vulns <target>

# Check for common CVEs
nmap --script "vuln and safe" -sV <target>

# === SPECIFIC SERVICES ===

# Full SMB enumeration
nmap -p 445 --script "smb-enum*,smb-vuln*,smb-os-discovery" <target>

# Web server full enum
nmap -p 80,443,8080,8443 --script "http-*" <target>

# === EVASION ===

# Stealthy scan through firewall
nmap -sS -T2 -f --source-port 53 -D RND:5 <target>

# Scan from inside (post-exploitation)
nmap -sT -Pn -n --disable-arp-ping -p- <internal_target>
```

---

## 📋 Pentest Methodology with Nmap

```
Step 1: Host Discovery
  nmap -sn <network>/24

Step 2: Quick Port Scan (top ports)
  nmap -T4 --top-ports 1000 -sV <target>

Step 3: Full Port Scan
  nmap -T4 -p- --min-rate 5000 <target>

Step 4: Detailed Service Scan (on found ports)
  nmap -sC -sV -p <ports> -oA detailed <target>

Step 5: Vulnerability Scan
  nmap --script vuln -p <ports> <target>

Step 6: Targeted NSE Scripts
  Based on services found (SMB, HTTP, FTP, etc.)
```

---

## ⚠️ Common Mistakes

| Mistake | Fix |
|---------|-----|
| Scan seems slow | Add `-n` (no DNS), use `-T4`, add `--min-rate` |
| Host shows as "down" | Use `-Pn` to skip host discovery |
| Missing ports | Use `-p-` for full scan, not just top 1000 |
| UDP services missed | Add `-sU` (separate scan recommended) |
| Results inconsistent | Lower timing, increase retries |

---

## 🔗 Links

- After Nmap → enumerate specific services (SMB, HTTP, LDAP)
- Combine with: Masscan (faster initial sweep), then Nmap for details
- Alternative: RustScan (faster port discovery, feeds into Nmap)
