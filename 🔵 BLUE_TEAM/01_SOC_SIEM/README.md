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
* **Alert Fatigue**: A state where analysts are overwhelmed by too many FPs, leading to missed TPs.

---

## 🏛️ The Three Pillars of SOC Maturity

For a defensive environment to be effective, these three elements must be balanced:
1.  **People**: Skilled analysts (L1-L3) and engineers.
2.  **Process**: Standard Operating Procedures (SOPs) and Incident Response playbooks.
3.  **Technology**: SIEM, EDR, IDS, and automated SOAR platforms.

---

## 👥 SOC Role Hierarchy & Personnel

The SOC operates through a tiered structure to ensure efficient alert handling:

| Tier | Role | Primary Responsibility |
| :--- | :--- | :--- |
| **L1** | **Triage Analyst** | Initial monitoring, filtering false positives, and basic ticket creation. |
| **L2** | **Incident Responder** | Deep-dive investigation of validated alerts, containment, and forensics. |
| **L3** | **Threat Hunter** | Proactive search for undetected threats and advanced malware analysis. |
| **Eng** | **Detection Engineer** | Managing the SIEM stack and writing detection logic/YARA/Sigma rules. |

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

## 🔐 Infrastructure Trust: The Digital Chain of Trust

Security analysts must understand how trust is established on the web to identify certificate-based attacks.

* **Root CAs**: The ultimate "Trusted Sources" (e.g., DigiCert, Let's Encrypt). Their public keys are pre-installed in your OS/Browser.
* **Intermediate CAs**: Entities authorized by Root CAs to sign certificates on their behalf (improves security by keeping Root keys offline).
* **End-Entity Certificate**: The final certificate assigned to a website or server.
* **Validation**: If any link in this chain is broken or signed by an unknown entity, the "Chain of Trust" is invalid (warning the user of a potential MitM).

---

## 🔗 Associated Tools
* [Splunk](../TOOLS/splunk.md) - Industry-leading SIEM and data analytics.
* [Snort](../TOOLS/snort.md) - Network Intrusion Detection System (NIDS).
* [Wireshark](../TOOLS/wireshark.md) - Deep packet analysis.
