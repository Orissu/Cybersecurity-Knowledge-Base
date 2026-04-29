# 🛡️ Network Defense & Hardening

> **Philosophy:** Security is not a product, but a process. A resilient network relies on multiple layers of overlapping controls.

---

## 🏗️ Defense in Depth (DiD)
The goal of **Defense in Depth** is to ensure that if one control fails, others are in place to stop the threat.



* **Administrative Controls**: Security awareness training, policies, and procedures.
* **Physical Controls**: Locks, CCTV, and data center security.
* **Technical Controls**:
    * **Firewalls**: "Security Guards" that enforce access control lists (ACLs) based on IP/Port.
    * **IDS (Intrusion Detection System)**: "Surveillance Cameras" that monitor internal traffic for signatures of known attacks.
    * **IPS (Intrusion Prevention System)**: An IDS that can actively block malicious traffic in real-time.

---

## 📡 Traffic Analysis Tools

| Tool | Type | Primary Use Case |
| :--- | :--- | :--- |
| **Wireshark** | Passive GUI | Deep-dive forensic investigation and protocol education. No active alerting. |
| **TCPdump** | Passive CLI | High-performance packet capture on servers or headless devices. |
| **Snort** | Active/Passive | Real-time traffic monitoring and signature-based alerting. |

---

## 🧱 Firewall Architecture
Firewalls act as the primary "Security Guards" of the network, but their effectiveness depends on their inspection mechanics.



### Stateless vs. Stateful Inspection
| Feature | Stateless Firewall | Stateful Firewall |
| :--- | :--- | :--- |
| **Logic** | Matches each packet against rules independently. | Tracks the "state" and context of active connections. |
| **Context** | No memory of previous packets. | Understands the relationship between packets (e.g., ACK vs SYN). |
| **Performance** | **Very High Speed** (low overhead). | Moderate (requires CPU/RAM to track states). |
| **Security** | Lower (vulnerable to spoofing/fragmentation). | Higher (can block unsolicited incoming traffic). |

---

## 🚨 Intrusion Detection Systems (IDS)
If firewalls are the guards, the IDS is the **Surveillance Camera**. It detects threats that bypass firewalls via legitimate-looking connections (e.g., a SQL injection over Port 80).

### IDS Categories
* **NIDS (Network IDS)**: Monitors traffic across the entire network (e.g., Snort).
* **HIDS (Host IDS)**: Monitors activity on a specific device (e.g., OSSEC, Wazuh).

### Detection Methodologies
1.  **Signature-Based**: Matches traffic against a database of known attack patterns (fast, but misses New/Zero-day threats).
2.  **Anomaly-Based**: Uses a "Baseline" of normal behavior and flags deviations (good for Zero-days, but prone to False Positives).
3.  **Hybrid**: Combines both for maximum coverage.

---

## 📡 Analysis vs. Defense
It is critical to distinguish between **Passive Analysis** and **Active Defense**:

* **Wireshark (Passive Sniffing)**: Ideal for forensics and deep-dive protocol education. It does **not** modify data streams or alert in real-time.
* **Snort (IDS)**: Actively scans traffic against rules and generates alerts for the SOC.

---

## 🔗 Associated Tools
* [TCPdump](../TOOLS/tcpdump.md) - CLI Packet Sniffer.
* [Snort](../TOOLS/snort.md) - Network IDS/IPS.
* [Wireshark](../TOOLS/wireshark.md) - Protocol Analyzer.
