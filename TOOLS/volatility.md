# 🛠️ Volatility Framework

> **Category:** Memory Forensics & Incident Response
> **When to Use:** Analyzing RAM dumps for malware, credentials, network connections, and hidden processes.

---

## 📋 Basic Syntax (Volatility 3)

```bash
vol -f <memory_dump> <plugin>
python3 vol.py -f <memory_dump> <plugin>
```

---

## 🔍 Essential Plugins

### System Information
```bash
# OS info and profile
vol -f dump.raw windows.info

# Registry hives
vol -f dump.raw windows.registry.hivelist
```

### Process Analysis
```bash
# List running processes
vol -f dump.raw windows.pslist

# Process tree (parent-child relationships)
vol -f dump.raw windows.pstree

# Hidden/unlinked processes (rootkit detection)
vol -f dump.raw windows.psscan

# Process command lines
vol -f dump.raw windows.cmdline

# DLLs loaded by a process
vol -f dump.raw windows.dlllist --pid <PID>

# Process environment variables
vol -f dump.raw windows.envars --pid <PID>
```

### Network Analysis
```bash
# Active network connections
vol -f dump.raw windows.netscan

# Network connections (older method)
vol -f dump.raw windows.netstat
```

### Malware Detection
```bash
# Find injected code (CRITICAL for malware)
vol -f dump.raw windows.malfind

# Detect API hooks
vol -f dump.raw windows.ssdt

# Loaded kernel modules
vol -f dump.raw windows.modules

# Detect hidden drivers
vol -f dump.raw windows.driverscan
```

### Credential Extraction
```bash
# Dump password hashes (SAM)
vol -f dump.raw windows.hashdump

# Dump cached credentials
vol -f dump.raw windows.cachedump

# LSA secrets
vol -f dump.raw windows.lsadump
```

### File System
```bash
# List open files
vol -f dump.raw windows.filescan

# Dump a specific file from memory
vol -f dump.raw windows.dumpfiles --virtaddr <address>

# MFT entries
vol -f dump.raw windows.mftscan
```

### Registry
```bash
# List registry hives
vol -f dump.raw windows.registry.hivelist

# Print specific registry key
vol -f dump.raw windows.registry.printkey --key "Software\Microsoft\Windows\CurrentVersion\Run"

# Dump user assist (program execution history)
vol -f dump.raw windows.registry.userassist
```

---

## 📋 Investigation Workflow

```
1. Identify the OS:
   vol -f dump.raw windows.info

2. List processes (look for suspicious names/parents):
   vol -f dump.raw windows.pstree

3. Check network connections (C2 callbacks):
   vol -f dump.raw windows.netscan

4. Look for injected code:
   vol -f dump.raw windows.malfind

5. Dump suspicious processes:
   vol -f dump.raw windows.pslist --dump --pid <PID>

6. Extract credentials:
   vol -f dump.raw windows.hashdump

7. Check persistence (Run keys):
   vol -f dump.raw windows.registry.printkey --key "...\Run"
```

---

## 🚩 Red Flags to Look For

| Indicator | What It Means |
|-----------|---------------|
| `cmd.exe` spawned by `notepad.exe` | Process injection |
| `svchost.exe` not under `services.exe` | Masquerading malware |
| Process with no parent | Unlinked/hidden process |
| Connections to unusual IPs/ports | C2 communication |
| `malfind` shows MZ header in memory | Injected PE file |
| Multiple `rundll32.exe` instances | Possible malware execution |
| Process running from `\Temp\` or `\AppData\` | Suspicious location |

---

## 💡 Expert Tips

| Tip | Details |
|-----|---------|
| Always start with `windows.info` | Confirms OS and architecture |
| Compare `pslist` vs `psscan` | Differences reveal hidden processes |
| Check parent-child relationships | `pstree` reveals anomalies fast |
| Dump suspicious processes | Analyze with strings, YARA, or VirusTotal |
| Timeline analysis | Correlate process creation times with network activity |

---

## 🔗 Links

- Before Volatility → acquire memory dump (FTK Imager, WinPMEM, LiME)
- After analysis → correlate with disk forensics and network logs
- Combine with: YARA rules (scan memory for known malware signatures)
- Alternative: Rekall (similar framework, less maintained)
