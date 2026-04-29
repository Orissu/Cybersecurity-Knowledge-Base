# 🛠️ Nmap (Network Mapper)

> **Usage:** Host discovery, port scanning, and service enumeration.

## 📋 Target Specification
* **Individual IP**: `nmap 192.168.1.1`
* **Hostname**: `nmap target.com`
* **Range**: `nmap 192.168.1.1-50`
* **Subnet (CIDR)**: `nmap 192.168.1.0/24`

## 📋 Target Discovery & Stealth
| Flag | Description | Use Case |
| :--- | :--- | :--- |
| `-sn` | **Ping Scan** | Host discovery only. No port scanning. |
| `-Pn` | **No Ping** | Treats all hosts as online. Bypasses firewalls blocking ICMP. |
| `-n` / `-R` | **DNS Control** | `-n` (Never do DNS resolution) / `-R` (Always resolve). |
| `-p-` | **Full Port Scan** | Scans all 65,535 ports (Default is top 1,000). |
| `-sS` | **TCP SYN Scan** | "Half-open" scan. Stealthier as it doesn't complete the handshake. |
| `-sU` | **UDP Scan** | Targets UDP services (DNS, SNMP, DHCP). Very slow. |

## 🔍 Service & OS Fingerprinting
Nmap sends specific probes and compares the "TCP/IP stack fingerprint" against its database.
* `-sV`: **Service Versioning**. Inspects open ports to determine what software is running.
* `-O`: **OS Detection**. Attempts to identify the operating system.
* `-A`: **Aggressive**. Combines `-sV`, `-O`, traceroute, and default scripts.


## ⚡ Performance & Timing
Adjusting these is critical for stability and speed.
* `-T[0-5]`: **Timing Templates**. `T4` is recommended for stable networks; `T2` for stealth/fragile systems.
* `--min-rate <number>`: Forces Nmap to send at least X packets per second.
* `--max-retries <number>`: Limits how many times Nmap re-probes a port (Default is 10). Set to `1` or `0` for speed.

## 🎭 Firewall / IDS Evasion
| Flag | Description | Use Case |
| :--- | :--- | :--- |
| `-f` | **Fragment Packets** | Splits IP packets to bypass simple packet filters. |
| `-D <R1,R2,ME>` | **Decoy Scan** | Masks your IP by spoofing other "decoy" IPs in the scan. |
| `--source-port <port>` | **Spoof Source Port** | Use `53` or `88` to trick firewalls into thinking the traffic is DNS/Kerberos. |
| `-mtu <value>` | **Custom MTU** | Sets a specific Maximum Transmission Unit (must be multiple of 8). |

## 📜 Nmap Scripting Engine (NSE)
Automates vulnerability detection and exploitation.
* `-sC`: Runs all **Default** scripts.
* `--script=<category>`: e.g., `vuln`, `exploit`, `auth`, `brute`.
* `--script-help=<script>`: Shows documentation for a specific script.

## 💡 Expert One-Liners
**The "Fast & Loud" internal scan:**
`nmap -T4 -p- -n -v --min-rate 5000 <target>`

**The "Vulnerability Audit":**
`nmap -Pn -sV --script vuln <target>`

## 💡 Expert Tip
Always start with a simple `nmap -sn <network>` to map the landscape. Once targets are identified, use `-Pn` if the host seems "down" despite being active, as many modern firewalls drop stealthy probes.
