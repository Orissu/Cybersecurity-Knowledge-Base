# 🔍 Threat Hunting

> **Objective:** Proactively search for threats that bypass automated detection. Don't wait for alerts — go find the attacker.

---

## 📋 Threat Hunting Methodology

```
1. Hypothesis — "An attacker may be using PowerShell for C2"
2. Data Collection — Gather relevant logs (Sysmon, PowerShell, network)
3. Investigation — Search for indicators matching hypothesis
4. Resolution — Confirm/deny hypothesis, document findings
5. Automation — Turn confirmed findings into detection rules
```

---

## 🗺️ MITRE ATT&CK Framework

### Tactic Categories (Attack Lifecycle)
| ID | Tactic | Description | Example Technique |
|----|--------|-------------|-------------------|
| TA0001 | Initial Access | Getting in | Phishing, exploit public app |
| TA0002 | Execution | Running code | PowerShell, cmd, WMI |
| TA0003 | Persistence | Staying in | Registry Run keys, services |
| TA0004 | Privilege Escalation | Getting higher access | Token manipulation, exploits |
| TA0005 | Defense Evasion | Avoiding detection | Obfuscation, log clearing |
| TA0006 | Credential Access | Stealing creds | Mimikatz, keylogging |
| TA0007 | Discovery | Learning the environment | net commands, BloodHound |
| TA0008 | Lateral Movement | Moving to other systems | PsExec, RDP, WinRM |
| TA0009 | Collection | Gathering target data | Screenshots, keylogging |
| TA0011 | Command & Control | Communicating with implant | HTTP C2, DNS tunneling |
| TA0010 | Exfiltration | Stealing data out | HTTP POST, DNS, cloud |
| TA0040 | Impact | Causing damage | Ransomware, wiper |

### Using ATT&CK for Hunting
```
1. Pick a technique (e.g., T1059.001 — PowerShell)
2. Understand what artifacts it leaves
3. Build a query to find those artifacts
4. Search your environment
5. Investigate anomalies
```

---

## 🎯 Hunting Hypotheses by Category

### Persistence Hunting
```
Hypothesis: "Attacker established persistence via registry Run keys"

Data sources:
- Sysmon Event 13 (Registry value set)
- Registry hive analysis

Query (Splunk):
index=sysmon EventCode=13 TargetObject="*\\CurrentVersion\\Run*"
| stats count by Image, TargetObject, Details
| where count < 5
```

### Lateral Movement Hunting
```
Hypothesis: "Attacker is using PsExec for lateral movement"

Data sources:
- Windows Event 7045 (new service installed)
- Sysmon Event 1 (process creation)
- Network connections (SMB port 445)

Query (Splunk):
index=windows EventCode=7045 ServiceName="PSEXESVC"
| table _time, ComputerName, ServiceName, ImagePath

# Also look for:
index=sysmon EventCode=1 Image="*\\psexec*"
```

### C2 Hunting
```
Hypothesis: "Attacker has C2 beaconing over HTTP/HTTPS"

Indicators:
- Regular interval connections (every 60s, 5min, etc.)
- Connections to newly registered domains
- High volume of DNS queries to single domain
- Unusual User-Agent strings

Query (Splunk):
index=proxy
| stats count, avg(bytes_out) as avg_bytes, stdev(bytes_out) as std_bytes by src_ip, dest
| where count > 100 AND std_bytes < 100
# Low standard deviation = beaconing (consistent size)
```

### Credential Access Hunting
```
Hypothesis: "Attacker dumped credentials from LSASS"

Data sources:
- Sysmon Event 10 (Process Access to lsass.exe)
- Sysmon Event 1 (suspicious process creation)

Query (Splunk):
index=sysmon EventCode=10 TargetImage="*\\lsass.exe"
| where NOT match(SourceImage, "(?i)(csrss|services|svchost|wininit|MsMpEng)")
| table _time, Computer, SourceImage, GrantedAccess
```

---

## 🚩 Indicators of Compromise (IOCs)

### Types of IOCs
| Type | Example | Detection Method |
|------|---------|-----------------|
| File hash (MD5/SHA256) | Known malware hash | EDR, YARA |
| IP address | C2 server IP | Firewall logs, proxy |
| Domain | Malicious domain | DNS logs, proxy |
| URL | Phishing/malware URL | Proxy logs |
| Email address | Phishing sender | Email gateway |
| Registry key | Persistence mechanism | Sysmon, registry monitoring |
| Mutex | Malware identifier | Process monitoring |
| User-Agent | Custom C2 UA string | Proxy logs |

### IOC Formats
| Format | Description | Used By |
|--------|-------------|---------|
| STIX | Structured Threat Information eXpression | MISP, threat intel platforms |
| TAXII | Transport protocol for STIX | Automated IOC sharing |
| OpenIOC | Mandiant's IOC format | YARA-like indicators |
| CSV | Simple list | Quick sharing |

### Threat Intelligence Sources
| Source | Type | URL |
|--------|------|-----|
| AlienVault OTX | Community IOCs | otx.alienvault.com |
| VirusTotal | File/URL analysis | virustotal.com |
| AbuseIPDB | Malicious IPs | abuseipdb.com |
| URLhaus | Malicious URLs | urlhaus.abuse.ch |
| MalwareBazaar | Malware samples | bazaar.abuse.ch |
| MISP | Sharing platform | Self-hosted |
| Shodan | Infrastructure intel | shodan.io |

---

## 🔍 Hunting Techniques

### Frequency Analysis (Stack Counting)
```
# Find rare processes (outliers = suspicious)
index=sysmon EventCode=1
| stats count by Image
| sort count
| head 20
# Rare processes across many hosts = likely malicious
```

### Baselining
```
# Establish normal → find abnormal
# Example: Normal DNS queries per host
index=dns
| stats count by src_ip
| eventstats avg(count) as avg_dns, stdev(count) as std_dns
| where count > (avg_dns + 3*std_dns)
# Hosts with 3x standard deviation above average = suspicious
```

### TTP-Based Hunting
```
# Hunt for specific MITRE techniques:

# T1053 — Scheduled Tasks (persistence)
index=sysmon EventCode=1 Image="*\\schtasks.exe" CommandLine="*/create*"

# T1021.002 — SMB/Windows Admin Shares
index=windows EventCode=5140 ShareName="\\\\*\\C$"

# T1003 — Credential Dumping
index=sysmon EventCode=10 TargetImage="*\\lsass.exe" GrantedAccess="0x1010"

# T1071 — Application Layer Protocol (C2)
index=proxy http_method=POST
| stats count by src_ip, dest
| where count > 50
```

---

## 📈 Pyramid of Pain (Detection Value)

```
┌─────────────────────────────────────┐
│         TTPs (Most Valuable)        │ ← Forces attacker to change methodology
├─────────────────────────────────────┤
│              Tools                  │ ← Forces new tooling
├─────────────────────────────────────┤
│      Network/Host Artifacts         │ ← Moderate effort to change
├─────────────────────────────────────┤
│          Domain Names               │ ← Easy to get new domains
├─────────────────────────────────────┤
│          IP Addresses               │ ← Trivial to change (VPN/proxy)
├─────────────────────────────────────┤
│     Hash Values (Least Valuable)    │ ← One byte change = new hash
└─────────────────────────────────────┘

Strategy: Focus detection on TTPs, not just IOCs
```

---

## 🔗 Key Resources

| Resource | Purpose |
|----------|---------|
| attack.mitre.org | ATT&CK framework navigator |
| car.mitre.org | Cyber Analytics Repository (detection queries) |
| sigma-rule (GitHub) | Community detection rules |
| Atomic Red Team | Test detection with real techniques |
| LOLBAS / GTFOBins | Living-off-the-land binaries |

---

## 🔗 Associated Tools
- [Snort](../../TOOLS/snort.md) - Network detection
- [Volatility](../../TOOLS/volatility.md) - Memory analysis
- [TCPdump](../../TOOLS/tcpdump.md) - Traffic capture
