# 🚩 Red Team Operations

> **Objective:** Offensive security testing, vulnerability research, and adversary simulation. This section documents the full attack lifecycle with actionable commands.

---

## 📂 Section Hierarchy

| Section | Focus | Key Skills |
|---------|-------|-----------|
| [01_Recon_OSINT](./01_Recon_OSINT/) | Information gathering, attack surface mapping | Google Dorks, subdomain enum, OSINT |
| [02_WEB_Hacking](./02_WEB_Hacking/) | Web application exploitation | SQLi, XSS, LFI, SSRF, SSTI |
| [03_Network_Attacks](./03_Network_Attacks/) | Network exploitation, pivoting | Reverse shells, MITM, tunneling |
| [04_Linux_Priv_Esc](./04_Linux_Priv_Esc/) | Linux root escalation | SUID, sudo, cron, capabilities |
| [05_Windows_Priv_Esc](./05_Windows_Priv_Esc/) | Windows SYSTEM escalation | Services, tokens, credentials |
| [06_Active_Directory](./06_Active_Directory/) | Domain compromise | Kerberos, lateral movement, DCSync |

---

## 🚀 Attack Methodology (Kill Chain)

```
1. Reconnaissance
   └── Passive & Active discovery (OSINT, Nmap, web enum)

2. Initial Access
   └── Exploit web vuln, phishing, default creds, known CVE

3. Execution
   └── Reverse shell, web shell, code execution

4. Privilege Escalation
   └── Low-priv → root/SYSTEM (misconfigs, exploits)

5. Lateral Movement
   └── Pivot to other machines (PtH, PtT, WinRM)

6. Persistence
   └── Maintain access (backdoors, scheduled tasks, keys)

7. Exfiltration
   └── Extract target data (files, credentials, secrets)
```

---

## ⚡ Quick Start: Common Attack Flow

```bash
# 1. Scan target
nmap -sC -sV -p- -T4 TARGET

# 2. Enumerate web (if port 80/443)
ffuf -u http://TARGET/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

# 3. Exploit vulnerability → get shell
nc -lvnp 4444    # listener

# 4. Stabilize shell
python3 -c 'import pty;pty.spawn("/bin/bash")'
# Ctrl+Z → stty raw -echo; fg → export TERM=xterm

# 5. Enumerate for privesc
# Linux: sudo -l, find / -perm -4000, ./linpeas.sh
# Windows: whoami /priv, winpeas.exe

# 6. Escalate to root/SYSTEM

# 7. Loot (flags, credentials, sensitive data)
```

---

## 🔗 Main Tools
- [Nmap](../TOOLS/nmap.md) - Network scanning
- [Burp Suite](../TOOLS/burpsuite.md) - Web testing
- [Metasploit](../TOOLS/metasploit.md) - Exploitation
- [Netcat](../TOOLS/netcat.md) - Shells & transfer
- [LinPEAS](../TOOLS/LinPEAS.md) - Linux enumeration
- [Hashcat](../TOOLS/Hashcat.md) - Password cracking
- [Hydra](../TOOLS/hydra.md) - Brute-force
