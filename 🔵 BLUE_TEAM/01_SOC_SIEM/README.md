# 🛡️ SOC & SIEM Operations

> **Objective:** Monitor, detect, and respond to security incidents using centralized log analysis.

---

## 🏢 SOC Structure

### Analyst Tiers
| Tier | Role | Responsibilities |
|------|------|-----------------|
| L1 | Triage Analyst | Monitor alerts, filter false positives, escalate |
| L2 | Incident Responder | Deep investigation, containment, forensics |
| L3 | Threat Hunter | Proactive hunting, malware analysis, rule creation |
| Eng | Detection Engineer | SIEM management, write detection rules |

### Alert Classification
| Type | Meaning | Action |
|------|---------|--------|
| True Positive (TP) | Real attack detected | Investigate and respond |
| False Positive (FP) | Benign activity flagged | Tune the rule |
| True Negative (TN) | Normal activity, not flagged | Correct behavior |
| False Negative (FN) | Real attack missed | Most dangerous — improve detection |

---

## 🛰️ SIEM Fundamentals

### What a SIEM Does
```
1. Collect — Aggregate logs from all sources (endpoints, firewalls, servers)
2. Normalize — Convert different log formats into unified schema
3. Correlate — Link related events across sources and time
4. Alert — Trigger notifications when rules match
5. Retain — Store logs for compliance and investigation
6. Visualize — Dashboards for real-time monitoring
```

### Common SIEM Platforms
| Platform | Query Language | Notes |
|----------|---------------|-------|
| Splunk | SPL | Industry leader, expensive |
| Elastic/ELK | KQL / Lucene | Open-source, flexible |
| Microsoft Sentinel | KQL | Cloud-native, Azure integration |
| QRadar | AQL | IBM, enterprise-focused |
| Wazuh | Custom | Open-source HIDS + SIEM |

---

## 📊 Splunk (SPL) Quick Reference

### Basic Searches
```spl
# Search for failed logins
index=windows EventCode=4625

# Search by source IP
index=firewall src_ip="10.10.10.5"

# Search for PowerShell execution
index=windows EventCode=4104 ScriptBlockText="*Invoke-Mimikatz*"

# Time-based search
index=windows earliest=-24h latest=now EventCode=4625
```

### Filtering & Statistics
```spl
# Count events by source
index=windows EventCode=4625 | stats count by src_ip | sort -count

# Top 10 failed login sources
index=windows EventCode=4625 | top 10 src_ip

# Timeline of events
index=windows EventCode=4625 | timechart count by src_ip

# Filter specific fields
index=windows EventCode=4624 LogonType=10 | table _time, user, src_ip
```

### Correlation Examples
```spl
# Brute-force detection (>5 failures then success from same IP)
index=windows (EventCode=4625 OR EventCode=4624)
| stats count(eval(EventCode=4625)) as failures, count(eval(EventCode=4624)) as successes by src_ip
| where failures > 5 AND successes > 0

# Lateral movement (new logon from internal IP)
index=windows EventCode=4624 LogonType=3 NOT src_ip="127.0.0.1"
| stats count by src_ip, dest, user | where count < 3

# Data exfiltration (large outbound transfers)
index=firewall action=allowed bytes_out > 10000000
| stats sum(bytes_out) as total_bytes by src_ip, dest_ip
| where total_bytes > 100000000
```

---

## 📊 Elastic / KQL Quick Reference

```kql
# Failed logins
event.code: "4625" and winlog.event_data.TargetUserName: "admin"

# PowerShell suspicious commands
event.code: "4104" and winlog.event_data.ScriptBlockText: *downloadstring*

# Process creation with command line
event.code: "1" and process.command_line: *mimikatz*

# Network connections to unusual ports
destination.port > 1024 and not destination.port: (8080 or 8443 or 3389)
```

---

## 📝 Key Windows Event IDs

### Authentication
| Event ID | Description | Relevance |
|----------|-------------|-----------|
| 4624 | Successful logon | Track who logged in |
| 4625 | Failed logon | Brute-force detection |
| 4634 | Logoff | Session tracking |
| 4648 | Logon with explicit credentials | Lateral movement |
| 4672 | Special privileges assigned | Admin logon |
| 4720 | User account created | Persistence |
| 4732 | Member added to security group | Privilege escalation |

### Logon Types
| Type | Description | Suspicious When |
|------|-------------|----------------|
| 2 | Interactive (console) | Unusual hours |
| 3 | Network (SMB, WinRM) | From unexpected source |
| 4 | Batch | Unusual scheduled tasks |
| 7 | Unlock | After hours |
| 10 | RemoteInteractive (RDP) | From external IP |

### Process & Service
| Event ID | Description | Relevance |
|----------|-------------|-----------|
| 4688 | Process creation | Track execution |
| 4689 | Process exit | Correlate with creation |
| 7045 | Service installed | Persistence (PsExec) |
| 1102 | Audit log cleared | Anti-forensics |

### Sysmon (Enhanced Logging)
| Event ID | Description | Relevance |
|----------|-------------|-----------|
| 1 | Process creation (with hash + cmdline) | Malware execution |
| 3 | Network connection | C2 callbacks |
| 7 | Image loaded (DLL) | DLL injection |
| 8 | CreateRemoteThread | Process injection |
| 10 | Process access | Mimikatz (LSASS access) |
| 11 | File created | Malware drops |
| 13 | Registry value set | Persistence |
| 22 | DNS query | C2 domain resolution |

---

## 📜 Sigma Rules

### What is Sigma?
Generic signature format for SIEM detection rules. Write once, convert to any SIEM.

### Sigma Rule Structure
```yaml
title: Mimikatz Usage Detected
status: experimental
description: Detects Mimikatz execution via process creation
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        Image|endswith:
            - '\mimikatz.exe'
        CommandLine|contains:
            - 'sekurlsa'
            - 'lsadump'
            - 'privilege::debug'
    condition: selection
level: critical
tags:
    - attack.credential_access
    - attack.t1003
```

### Converting Sigma Rules
```bash
# Install sigmac / sigma-cli
pip install sigma-cli

# Convert to Splunk
sigma convert -t splunk -p sysmon rule.yml

# Convert to Elastic
sigma convert -t elasticsearch rule.yml

# Convert to QRadar
sigma convert -t qradar rule.yml
```

### Useful Sigma Rule Categories
| Category | Detects |
|----------|---------|
| Credential Access | Mimikatz, LSASS dump, SAM access |
| Lateral Movement | PsExec, WMI, WinRM from unusual sources |
| Persistence | New services, scheduled tasks, registry Run keys |
| Defense Evasion | Log clearing, AMSI bypass, timestomping |
| Exfiltration | Large DNS queries, unusual outbound traffic |

---

## 🔔 YARA Rules

### What is YARA?
Pattern matching tool for identifying malware in files and memory.

### YARA Rule Structure
```yara
rule Detect_Mimikatz {
    meta:
        description = "Detects Mimikatz binary"
        author = "Analyst"
        date = "2024-01-01"
    
    strings:
        $s1 = "mimikatz" ascii wide nocase
        $s2 = "sekurlsa::logonpasswords" ascii
        $s3 = "lsadump::dcsync" ascii
        $hex = { 4D 5A 90 00 }  // MZ header
    
    condition:
        $hex at 0 and (any of ($s*))
}
```

### Using YARA
```bash
# Scan a file
yara rule.yar suspicious_file.exe

# Scan a directory recursively
yara -r rule.yar /path/to/scan/

# Scan running processes
yara -p rule.yar

# Multiple rules
yara rules_directory/ target_file
```

---

## 🚨 Incident Response Framework (NIST)

```
1. Preparation
   - IR plan, playbooks, tools ready
   - Logging configured, baselines established

2. Detection & Analysis
   - Alert triage (TP vs FP)
   - Scope assessment (how far did attacker get?)
   - IOC identification

3. Containment, Eradication, Recovery
   - Short-term: Isolate affected systems
   - Long-term: Patch vulnerability, reset credentials
   - Eradicate: Remove malware, backdoors
   - Recover: Restore from clean backups

4. Post-Incident Activity
   - Lessons learned
   - Update detection rules
   - Improve procedures
```

### IR Quick Actions
```bash
# Isolate host (Windows)
netsh advfirewall set allprofiles firewallpolicy blockinbound,blockoutbound

# Collect volatile data before shutdown
# Memory dump → processes → network connections → logged users

# Preserve evidence
# Image disk before remediation
# Export relevant logs
# Screenshot active sessions
```

---

## 📈 Key Frameworks

### Pyramid of Pain
```
(Hardest to change — most impactful detection)
    TTPs (Tactics, Techniques, Procedures)
    Tools
    Network/Host Artifacts
    Domain Names
    IP Addresses
    Hash Values
(Easiest to change — least impactful detection)
```

### MITRE ATT&CK (map attacks to techniques)
```
Reconnaissance → Resource Development → Initial Access →
Execution → Persistence → Privilege Escalation →
Defense Evasion → Credential Access → Discovery →
Lateral Movement → Collection → C2 → Exfiltration → Impact
```

---

## 🔗 Associated Tools
- [Splunk](../../TOOLS/splunk.md) - SIEM platform
- [Snort](../../TOOLS/snort.md) - Network IDS
- [Volatility](../../TOOLS/volatility.md) - Memory forensics
