# 🛠️ Nmap (Network Mapper)

> **Category:** Network Discovery & Security Auditing
> **When to Use:** First tool in ANY engagement. Maps the attack surface before exploitation.

---
## About Nmap

Nmap uses protocols in order to scan the network, the machines and the ports. If no option has been given, nmap follows this pattern :
1) If the scan is on the same local network, nmap uses ARP if it's a priviledged user;
2) If the scan is outside the local network, nmap uses ICMP echo requests, TCP ACK port 80, TCP SYN port 443 and ICMP timestamp requests.
3) If the one using nmap is a regular user and outside the local network, nmap uses a TCP 3-way handshake (SYN to port 80 and port 443).
By default, nmap does a PING to discover live hosts and then scans live hosts only.
Only online hosts answer to a ARP scan.
Sometimes, firewalls block ICMP echo requests (-PE) or ICMP echo replies (type 8 and type 0) so we can use ICMP timestamp requests (-PP) or ICMP adress masks requests (-PM).
ICMP timestamp requests and replies are type 13 and 14 and ICMP address Mask requests and replies are type 17 and 18.

---
## About ports

Each service is linked to a TCP port, there are 65536 ports in total.
An open port means that there is a service listening on that port. A closed port doesn't have a service listening to it.
Because of firewalls, a port may seem closed even though there's a service listening on it.
There are 6 states of ports : 
- open
- closed
- filtered : A firewall, filter, or network obstacle blocks the probe, so Nmap cannot tell if the port is open or closed.
- unfiltered : The port is reachable, but Nmap cannot determine whether it is open or closed (typically seen only in ACK scan packets).
- open | filtered :  Nmap cannot decide whether the port is open or filtered (such as in UDP, IP proto, FIN, Null, or Xmas scans).
- closed | filtered : Nmap cannot decide whether the port is closed or filtered (used only in IP ID idle scans).

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
| `-sM` | Maimon Scan | FIN+ACK flags | detect open or closed ports (won't work in modern networks) |
| `--scanflags` | Custom Flag Scan | Let's you choose the flag you want in the scan |

The NULL Scan, FIN Scan and Xmas Scan doesn't trigger a response when they reach an open port.
When they reach a closed port, the response is a RST,ACK.
The Xmas Scan is efficient when scanning a target behind a stateless firewall. A Stateless firewall checks whether the incoming packet has the SYN flag set to detect a connection packet.

---

## Spoofing and decoys

In some networks setups, it is possible to scan a target using a spoofed IP address and even a spoofed MAC address.
It is only benefitial if it is guaranteed to have a response.
The attacker (A) scans the target (B) using the spoofed IP address of (C). (B) then answer to (C) and (A) can have the response by monitoring (C) beforehand.
to use it we use `-S @IP TARGET`
It's useless if (A) cannot monitor the network for responses. If the scan is in the same subnet then it is possible to spoof the MAC address with `--spoof-mac`

To use a decay scan we use `-D` with a specific IP address or multiple ones. We can add me to indicate the position of our IP address :
`nmap -D IP1,IP2,ME,IP3,RND,RND TARGET`. The RND means that the IP address will be at random.

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
| `-PR` | ARP scan | discover live hosts on the same local network |
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
| `--open` | open ports only |

---

## 🔍 Service & OS Detection

| Flag | Description | Use Case |
|------|-------------|----------|
| `-sV` | Service version detection | Identify software and version on open ports (not compatible with -sS, forces a 3-way handshake) |
| `--version-intensity <0-9>` | Version scan intensity | Higher = more accurate but slower |
| `-sV --version-light` | level 2 | --- |
| `-sV --version-all` | level 9 | --- |
| `-O` | OS detection | Identify target operating system |
| `--traceroute` | network mapping |Find the routers between the host and the target |
| `-A` | Aggressive scan | Combines -sV, -O, traceroute, default scripts |
| `--oscan-guess` | Guess OS more aggressively | When OS detection is uncertain |

If nmap doesn't succeed to find the OS, there's still useful clues like the TTL, TCP sequences, service responses (banners and protocol quirks).
The absence of an exact match can be due to : Virtualization, firewall, cloud networking layers,  kernel customisation or network middleboxes that modify packet characteristics. It should be interpreted carefully as nowadays because of the new technologies altering information.

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
| `-r` | Order | To scan in consecutive order |
| `--min-rate <n>` | Minimum packets/sec | Force speed (e.g., 5000) |
| `--max-rate <n>` | Maximum packets/sec | Throttle to avoid detection |
| `--max-retries <n>` | Max probe retries | Lower = faster (default 10) |
| `--host-timeout <time>` | Give up on slow hosts | Skip unresponsive targets |

---

## 📜 Nmap Scripting Engine (NSE)

A script is a piece of code that doesn't need to be compiled.
For nmap, it enables adding custom functionalities that weren't available via built-in cmds. Nmap default install's contains up to 600 scripts.
They're in the directory `/usr/share/nmap/scripts` and we can choose to use default scripts zith `--script=default or -sC`.
There are lots of other categories.

| Script Category | Description |
|---|---|
| `auth` | Runs authentication-related scripts |
| `broadcast` | Discovers hosts by sending broadcast messages |
| `brute` | Performs brute-force password auditing against logins |
| `default` | Runs default scripts (same as `-sC`) |
| `discovery` | Retrieves accessible information, such as database tables and DNS names |
| `dos` | Detects servers vulnerable to Denial of Service (DoS) |
| `exploit` | Attempts to exploit various vulnerable services |
| `external` | Checks using a third-party service, such as Geoplugin and Virustotal |
| `fuzzer` | Launches fuzzing attacks |
| `intrusive` | Runs intrusive scripts such as brute-force attacks and exploitation |
| `malware` | Scans for backdoors |
| `safe` | Runs safe scripts that won't crash the target |
| `version` | Retrieves service versions |
| `vuln` | Checks for vulnerabilities or exploits in a vulnerable service |



| Flag | Description |
|------|-------------|
| `-sC` | Run default scripts |
| `--script=<name>` | Run specific script |
| `--script=<category>` | Run category (vuln, exploit, auth, brute, discovery) |
| `--script-args=<args>` | Pass arguments to scripts |
| `--script-help=<name>` | Show script documentation |

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

to fragment packets, we use -f, it fragments the data into packets of 8 MB or lower. if we use -ff, the packets are 16MB or lower for the last one.

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

## Idle/Zombie scan

To use it we can use `-sI ZOMBIE TARGET`.
The purpose of this scan is to probe a target using the IP address of a Zombie host (printer or something else...). Therefore, the target system sees each probe appearing to come from the idle/zombie host. Then we can check if the zombie host received a response by checking the IP ID in the header.
It requires an idle system connected to the network that we can communicate with.

It requires 3 steps to set up the scan : 
-Trigger the idle host to respond to the host with the zombie's IPID
-Send a SYN packet to the target (spoofed zombie)
-Trigger the zombie host again to compare the new IPID

--reason to add more details to reasoning and conclusions. -d for debbugging details and -dd for even more details.

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
