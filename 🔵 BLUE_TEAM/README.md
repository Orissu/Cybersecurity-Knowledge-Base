# 🛡️ Blue Team Operations

> **Objective:** Defensive security — detect, respond to, and prevent attacks. Understanding Blue Team makes you a better Red Teamer.

---

## 📂 Section Hierarchy

| Section | Focus | Key Skills |
|---------|-------|-----------|
| [01_SOC_SIEM](./01_SOC_SIEM/) | Security Operations, log analysis, alerting | Splunk, ELK, Sigma rules, triage |
| [02_Digital_Forensics](./02_Digital_Forensics/) | Evidence acquisition and analysis | Volatility, Autopsy, artifact analysis |
| [03_Network_Defense](./03_Network_Defense/) | Network monitoring and hardening | Firewalls, IDS/IPS, segmentation |
| [04_Threat_Hunting](./04_Threat_Hunting/) | Proactive threat detection | MITRE ATT&CK, IOCs, hypothesis-driven hunting |

---

## 📊 Defensive Mindset

```
1. Visibility — If it isn't logged, it didn't happen
2. Detection — Know what normal looks like to spot abnormal
3. Response — Contain fast, investigate thoroughly
4. Prevention — Harden before the attack, not after
5. Improvement — Every incident is a lesson
```

---

## 🔄 Why Blue Team Matters for Pentesters

| Red Team Action | Blue Team Detection |
|----------------|-------------------|
| Nmap scan | Firewall logs, IDS alerts |
| Brute-force | Failed login events, account lockouts |
| Reverse shell | Outbound connection to unusual port |
| Mimikatz | LSASS access event (Sysmon Event 10) |
| Lateral movement | Logon Type 3 from unusual source |
| Data exfiltration | Large outbound transfers, DNS tunneling |

Understanding detection helps you evade it. Understanding evasion helps you detect it.

---

## 🔗 Main Tools
- [Volatility](../TOOLS/volatility.md) - Memory forensics
- [Snort](../TOOLS/snort.md) - Network IDS
- [TCPdump](../TOOLS/tcpdump.md) - Packet capture
