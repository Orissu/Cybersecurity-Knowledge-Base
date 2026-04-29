# 🛡️ SOC & SIEM Operations

> **Overview:** Blue Teaming is the proactive and reactive defense of an organization's internal infrastructure. It relies on the synergy between the **SOC (Security Operations Center)**—the people and processes—and the **SIEM (Security Information and Event Management)**—the technology.

---

## 🏛️ Core Pillars of Blue Teaming

A comprehensive defensive strategy is built on five functional areas:

1.  **Monitoring & Detection**: Continuous oversight of network traffic and system logs. The goal is to identify **anomalies** or deviations from established behavioral baselines.
2.  **Incident Response (IR)**: The tactical "firefighting" process of containing, eradicating, and recovering from a confirmed breach.
3.  **Threat Intelligence**: Proactive analysis of external data (IP reputations, file hashes, and **TTPs**) to anticipate emerging attack vectors.
4.  **Vulnerability Management**: Identifying, prioritizing, and patching security weaknesses before they are exploited.
5.  **Digital Forensics & Investigation**: The post-incident deep dive into logs, memory, and disk images to determine the *how* and *why* of an attack.

---

## 🏢 The SOC (Security Operations Center)

The SOC is a 24/7 frontline facility dedicated to real-time security posture management.

### The Alert Triage Workflow
The primary mission of a SOC Analyst during triage is to filter the "noise":
* **True Positive (TP)**: A legitimate security incident that requires immediate action.
* **False Positive (FP)**: A benign activity incorrectly flagged as a threat (requires rule tuning).
* **True Negative (TN)**: Normal activity that is correctly ignored.
* **False Negative (FN)**: A real attack that bypasses detection (the most dangerous scenario).

---

## 🛰️ SIEM: The Defensive Radar

A **SIEM** (e.g., Splunk, Sentinel, ELK) aggregates and correlates data from across the enterprise.

### Key Capabilities:
* **Data Aggregation**: Collecting logs from Firewalls, Endpoints (EDR), Servers, and Applications.
* **Correlation**: Linking seemingly unrelated events (e.g., a failed login on a VPN followed by a PowerShell execution on a server).
* **Retention**: Storing historical data for compliance and forensic investigations.
* **Dashboards & Alerting**: Visualizing the threat landscape and notifying analysts of critical events.

---

## 📈 Operational Frameworks (Expert Context)

### NIST Incident Response Life Cycle
To handle an incident professionally, the SOC follows these four phases:
1.  **Preparation**: Hardening systems and establishing IR procedures.
2.  **Detection & Analysis**: Using the SIEM to identify and validate the breach.
3.  **Containment, Eradication, & Recovery**: Neutralizing the threat and restoring services.
4.  **Post-Incident Activity**: "Lessons Learned" to improve future defenses.

### The Pyramid of Pain
Defenders use this to measure the effectiveness of their **Threat Intelligence**:
* *Easy to change:* Hash values, IP addresses.
* *Challenging:* Domain names, Network/Host artifacts.
* *Tough for the Attacker:* **TTPs (Tactics, Techniques, and Procedures)**. If you detect TTPs, you force the attacker to reinvent their entire methodology.

---

## 🔗 Associated Tools
* [Splunk](../TOOLS/splunk.md) - Industry-leading SIEM and data analytics.
* [Snort](../TOOLS/snort.md) - Network Intrusion Detection System (NIDS).
* [Wireshark](../TOOLS/wireshark.md) - Deep packet analysis.
