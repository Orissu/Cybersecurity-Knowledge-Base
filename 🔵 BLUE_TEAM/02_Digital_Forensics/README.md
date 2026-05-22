# 🔍 Digital Forensics

> **Objective:** Acquire, preserve, and analyze digital evidence to reconstruct attack timelines.

---

## 📋 Forensics Methodology

```
1. Identification — Determine what systems are affected
2. Preservation — Acquire evidence without altering it
3. Analysis — Examine artifacts to reconstruct events
4. Documentation — Record findings with chain of custody
5. Reporting — Present findings clearly
```

---

## ⚖️ Order of Volatility (Collect First → Last)

| Priority | Evidence | Lifespan | Tool |
|----------|----------|----------|------|
| 1 | CPU registers/cache | Nanoseconds | N/A |
| 2 | Memory (RAM) | Minutes | DumpIt, WinPMEM, LiME |
| 3 | Network connections | Seconds | netstat, ss |
| 4 | Running processes | Minutes | tasklist, ps |
| 5 | Disk (filesystem) | Years | FTK Imager, dd |
| 6 | Remote logs/backups | Years | SIEM exports |

---

## 🧠 Memory Forensics

### Acquisition
```bash
# Windows — DumpIt (run from USB)
DumpIt.exe

# Windows — WinPMEM
winpmem_mini_x64.exe output.raw

# Linux — LiME (kernel module)
insmod lime.ko "path=/tmp/memdump.lime format=lime"

# Linux — /proc/kcore (if accessible)
dd if=/proc/kcore of=/tmp/memory.raw
```

### Analysis with Volatility 3
```bash
# System info
vol -f dump.raw windows.info

# Process list (look for suspicious processes)
vol -f dump.raw windows.pslist
vol -f dump.raw windows.pstree

# Hidden processes (compare pslist vs psscan)
vol -f dump.raw windows.psscan

# Network connections (find C2)
vol -f dump.raw windows.netscan

# Injected code (malware detection)
vol -f dump.raw windows.malfind

# Command history
vol -f dump.raw windows.cmdline

# Dump credentials
vol -f dump.raw windows.hashdump

# Registry (persistence)
vol -f dump.raw windows.registry.printkey --key "Software\Microsoft\Windows\CurrentVersion\Run"
```

### Red Flags in Memory
| Finding | Indicates |
|---------|-----------|
| Process with no parent | Hidden/unlinked process |
| cmd.exe under notepad.exe | Process injection |
| svchost.exe not under services.exe | Masquerading |
| Connections to unusual IPs | C2 communication |
| MZ header in malfind output | Injected PE file |
| Multiple rundll32.exe | Possible malware |

---

## 💾 Disk Forensics

### Acquisition
```bash
# Create forensic image (bit-for-bit copy)
dd if=/dev/sda of=/evidence/disk.img bs=4M status=progress

# With hashing for integrity
dc3dd if=/dev/sda of=/evidence/disk.img hash=sha256 log=acquisition.log

# FTK Imager (Windows GUI) — creates E01 format
# Autopsy can also acquire images
```

### Key Windows Artifacts

#### File System
| Artifact | Location | Contains |
|----------|----------|----------|
| MFT | $MFT (NTFS root) | All file metadata, timestamps |
| USN Journal | $UsnJrnl | File change history |
| Prefetch | C:\Windows\Prefetch\ | Program execution evidence |
| Amcache | C:\Windows\appcompat\Programs\Amcache.hve | Executed programs + hashes |
| Shimcache | SYSTEM registry hive | Program execution (even deleted) |

#### Registry
| Hive | Location | Contains |
|------|----------|----------|
| SAM | C:\Windows\System32\config\SAM | Local user accounts + hashes |
| SYSTEM | C:\Windows\System32\config\SYSTEM | Services, drivers, hostname |
| SOFTWARE | C:\Windows\System32\config\SOFTWARE | Installed programs, Run keys |
| NTUSER.DAT | C:\Users\<user>\NTUSER.DAT | User-specific settings |
| UsrClass.dat | C:\Users\<user>\AppData\Local\Microsoft\Windows\UsrClass.dat | Shellbags (folder access) |

#### Persistence Locations (Registry)
```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKLM\SYSTEM\CurrentControlSet\Services
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell
```

#### User Activity
| Artifact | What It Shows |
|----------|---------------|
| Recent files | C:\Users\<user>\AppData\Roaming\Microsoft\Windows\Recent\ |
| Jump Lists | Recently accessed files per application |
| Shellbags | Folders accessed (even deleted ones) |
| Browser history | Sites visited, downloads |
| TypedPaths | Explorer address bar history |
| UserAssist | Programs run from Explorer (ROT13 encoded) |

### Key Linux Artifacts
| Artifact | Location | Contains |
|----------|----------|----------|
| Auth logs | /var/log/auth.log | Login attempts, sudo usage |
| Syslog | /var/log/syslog | System events |
| Bash history | ~/.bash_history | Commands executed |
| Cron logs | /var/log/cron.log | Scheduled task execution |
| Last logins | /var/log/wtmp (use `last`) | Login history |
| Failed logins | /var/log/btmp (use `lastb`) | Failed attempts |
| Web logs | /var/log/apache2/access.log | Web requests |
| SSH keys | ~/.ssh/ | Authorized keys (persistence) |

---

## 📡 Network Forensics

### PCAP Analysis
```bash
# Capture traffic
tcpdump -i eth0 -w evidence.pcap

# Read and filter
tcpdump -r evidence.pcap 'host 10.10.10.5 and port 4444'

# Extract files from PCAP (Wireshark)
# File → Export Objects → HTTP/SMB/TFTP

# Zeek (generate connection logs from PCAP)
zeek -r evidence.pcap
cat conn.log | zeek-cut id.orig_h id.resp_h id.resp_p
```

### What to Look For in Network Evidence
| Indicator | Possible Attack |
|-----------|----------------|
| Beaconing (regular intervals) | C2 communication |
| DNS to unusual domains | DNS tunneling / C2 |
| Large outbound transfers | Data exfiltration |
| Internal host scanning many ports | Lateral movement |
| SMB connections between workstations | Lateral movement |
| HTTP POST to external IP | Data exfiltration |

---

## 🔧 Forensic Tools

| Tool | Purpose | Type |
|------|---------|------|
| Autopsy | Disk forensics GUI | Free |
| FTK Imager | Disk imaging | Free |
| Volatility | Memory analysis | Free |
| KAPE | Artifact collection | Free |
| Eric Zimmerman tools | Windows artifact parsing | Free |
| Wireshark | Network analysis | Free |
| Zeek | Network metadata | Free |
| Plaso/log2timeline | Timeline creation | Free |

---

## 📋 Investigation Workflow

```
1. Acquire evidence (memory first, then disk)
2. Create timeline (Plaso/log2timeline)
3. Check for persistence (Run keys, services, cron)
4. Analyze processes (Volatility — pstree, malfind)
5. Check network connections (netscan, PCAP)
6. Examine user activity (browser, recent files, bash_history)
7. Look for lateral movement (Event 4624 Type 3, SMB)
8. Identify initial access vector
9. Document and report
```

---

## 🔗 Associated Tools
- [Volatility](../../TOOLS/volatility.md) - Memory forensics
- [TCPdump](../../TOOLS/tcpdump.md) - Packet capture
