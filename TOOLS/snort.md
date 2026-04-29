# 🛠️ Snort

> **Usage:** Open-source Network Intrusion Detection System (NIDS) and Industry-standard Open-source Network Intrusion Detection System (NIDS).

## 📝 Overview
Developed in 1998, Snort remains a cornerstone of network defense. It functions by comparing network traffic against a set of **Rule Files** to identify malicious activity via signature and anomaly detection.

## 📝 Core Logic
Snort uses **rules** to inspect traffic. It can operate in three modes: **Sniffer**, **Packet Logger**, and **Network Intrusion Detection**.

## 📋 Rule Structure
A standard Snort rule is composed of a **Header** and **Options**:
`alert tcp $EXTERNAL_NET any -> $HTTP_SERVERS 80 (msg:"Potential SQLi"; content:"UNION SELECT"; sid:1000001;)`

* **Action**: `alert`, `log`, `pass`, or `drop`.
* **Protocol**: `tcp`, `udp`, `icmp`, `ip`.
* **Variables**: `$EXTERNAL_NET`, `$HTTP_SERVERS`.
* **Options**: `msg` (the alert name), `content` (the payload pattern to find), `sid` (Snort ID).

## 📋 Rule Management
Snort's power lies in its flexibility regarding rule sets:

* **Default Rules**: Built-in packages covering known exploits, malware C2, and policy violations.
* **Custom Rules**: Users can define specific patterns to monitor unique internal assets or emerging threats.
* **Tuning**: Admins can disable noisy default rules to reduce **Alert Fatigue** and focus on high-fidelity detections.

## 💡 Expert Tip: Rule Logic
When writing a custom rule, always remember that Snort is **order-dependent**. It processes rules in the order they appear in the configuration. Ensure your "Drop" or "Reject" rules are prioritized over "Log" rules for effective prevention (if running in IPS mode).
## 💡 Expert Tip: Custom Rules
Creating custom rules is essential for detecting zero-day threats or specific internal policy violations. Use the `sid` range **1,000,000+** for local custom rules to avoid conflicts with official Snort rule sets.

---

**See Also:** [TCPdump](tcpdump.md) for raw packet capture before rule implementation.

