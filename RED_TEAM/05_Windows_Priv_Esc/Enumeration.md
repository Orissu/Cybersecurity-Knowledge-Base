# Windows PrivEsc — Enumeration

When we have a low-privilege shell on a Windows machine, the first step is always to understand where we are and what we can touch before attempting anything.

---

## First Commands to Run

| Command | What it tells you |
|---|---|
| `whoami` | Current username |
| `whoami /priv` | Privileges assigned to the account — look for SeImpersonatePrivilege, SeAssignPrimaryTokenPrivilege |
| `whoami /groups` | Group membership — look for Administrators, Remote Desktop Users |
| `whoami /all` | Everything at once |
| `net user %username%` | Detailed info on current user (last logon, password policy, group membership) |
| `net user` | List all local users |
| `net localgroup administrators` | Who is in the local admins group |
| `systeminfo` | OS version, hostname, hotfixes — useful for WES-NG kernel exploit matching |
| `hostname` | Machine name |

---

## Services Enumeration

Services are the most common privesc vector on Windows. The question is always: can I write to the binary, the config, or the registry key?

| Command | What it tells you |
|---|---|
| `sc query state= all` | List all services and their state |
| `wmic service get name,displayname,pathname,startmode` | Full list with binary paths — look for unquoted paths |
| `tasklist /svc` | Running processes and their associated services |
| `accesschk.exe /accepteula -uwcqv user <service>` | Check if current user has write access to a specific service |
| `accesschk.exe /accepteula -uwcqv "Authenticated Users" *` | Check all services for misconfigurations |

**Key things to look for:**
- Unquoted paths with spaces: `C:\Program Files\Some App\service.exe` → Windows tries `C:\Program.exe` first
- `SERVICE_ALL_ACCESS` or `SERVICE_CHANGE_CONFIG` → can reconfigure the service
- A service binary path that is writable by the current user

---

## Registry Enumeration

| Command | What it tells you |
|---|---|
| `reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated` | First AIE check (must be 1) |
| `reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated` | Second AIE check (both must be 1) |
| `reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"` | AutoLogon credentials — sometimes plaintext passwords here |
| `reg query HKLM /f password /t REG_SZ /s` | Broad search for passwords in HKLM |
| `reg query HKCU /f password /t REG_SZ /s` | Same in HKCU |
| `reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run` | Autoruns — if a listed binary is writable, replace it |
| `accesschk /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\<svc>` | Check if a service registry key is writable |

---

## Credential Hunting

| Command | What it tells you |
|---|---|
| `cmdkey /list` | Saved credentials — if admin is listed, can use runas /savecred |
| `dir /s *pass*` | Finds files with "pass" in the name anywhere on the system |
| `findstr /si password *.xml *.ini *.txt *.config` | Searches file contents for the word "password" |
| `dir /s C:\Windows\Repair\SAM` | Old SAM backup — if readable, extract with impacket-secretsdump |
| `dir /s *unattend.xml *sysprep.xml` | Unattended install files — sometimes contain plaintext credentials |
| `type C:\Windows\Panther\Unattend.xml` | Common location for leftover install credentials |

---

## Scheduled Tasks

| Command | What it tells you |
|---|---|
| `schtasks /query /fo LIST /v` | Full list of scheduled tasks — look for ones running as SYSTEM |
| `icacls <path to task script>` | Check if the script the task runs is writable |

If a task runs as SYSTEM and runs a script you can write to, append a reverse shell call at the end of the script and wait.

---

## Token Privileges

Always check this — service accounts running IIS, MSSQL, or similar frequently have these privileges.

```cmd
whoami /priv
```

| Privilege | What it enables |
|---|---|
| `SeImpersonatePrivilege` | Potato attacks (PrintSpoofer, RoguePotato, GodPotato) |
| `SeAssignPrimaryTokenPrivilege` | Potato attacks |
| `SeBackupPrivilege` | Read any file on the system |
| `SeRestorePrivilege` | Write any file on the system |
| `SeTakeOwnershipPrivilege` | Take ownership of any file |

---

## Filesystem Checks

| Command | What it tells you |
|---|---|
| `icacls "C:\path\to\file.exe"` | Check permissions on a specific file or folder |
| `icacls "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp"` | Check if startup folder is writable |
| `dir /s /b C:\*pass*.txt C:\*cred*.txt` | Find credential files |

---

## Automated Tools

Run these early — don't wait until you've exhausted manual checks.

| Tool | How to use |
|---|---|
| **WinPEAS** | `winpeas.exe` — covers almost everything, highlighted output shows high-confidence findings |
| **PrivescCheck** | PowerShell: `Invoke-PrivescCheck` — cleaner output than WinPEAS, good second pass |
| **WES-NG** | Feed it `systeminfo` output offline → matches against missing patches and known CVEs |
| **Metasploit local_exploit_suggester** | Only if you have a Meterpreter session — `use post/multi/recon/local_exploit_suggester` |

---

## Order of Operations (from the room)

1. `whoami /all`, `net user` — know who you are
2. Run WinPEAS or PrivescCheck immediately — don't skip this
3. Check services and scheduled tasks for writable binaries or scripts
4. Hunt for credentials in registry and filesystem
5. Check AlwaysInstallElevated
6. Check `whoami /priv` for impersonation privileges → Potato attacks
7. Kernel exploits last (WES-NG) — they can crash the machine
