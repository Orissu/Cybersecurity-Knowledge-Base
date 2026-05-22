# 🛡️ Network Defense & Hardening

> **Objective:** Protect network infrastructure through monitoring, segmentation, and hardening.

---

## 🏗️ Defense in Depth

```
Layer 1: Physical — Locks, cameras, access control
Layer 2: Network — Firewalls, IDS/IPS, segmentation
Layer 3: Host — Antivirus, EDR, patching, hardening
Layer 4: Application — WAF, input validation, secure coding
Layer 5: Data — Encryption, DLP, access controls
Layer 6: People — Security awareness, phishing training
```

---

## 🧱 Firewall Configuration

### iptables (Linux)
```bash
# View current rules
iptables -L -n -v

# Default policy: drop everything
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Allow established connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow SSH from specific IP
iptables -A INPUT -p tcp --dport 22 -s 192.168.1.100 -j ACCEPT

# Allow HTTP/HTTPS
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Allow loopback
iptables -A INPUT -i lo -j ACCEPT

# Log dropped packets
iptables -A INPUT -j LOG --log-prefix "DROPPED: "

# Block specific IP
iptables -A INPUT -s 10.10.10.5 -j DROP

# Rate limit (anti brute-force)
iptables -A INPUT -p tcp --dport 22 -m limit --limit 3/min --limit-burst 3 -j ACCEPT

# Save rules
iptables-save > /etc/iptables/rules.v4
```

### Windows Firewall (PowerShell)
```powershell
# View rules
Get-NetFirewallRule | Where-Object {$_.Enabled -eq 'True'}

# Block inbound from IP
New-NetFirewallRule -DisplayName "Block Attacker" -Direction Inbound -RemoteAddress 10.10.10.5 -Action Block

# Allow specific port
New-NetFirewallRule -DisplayName "Allow HTTPS" -Direction Inbound -Protocol TCP -LocalPort 443 -Action Allow

# Block all inbound (emergency isolation)
Set-NetFirewallProfile -Profile Domain,Public,Private -DefaultInboundAction Block
```

---

## 🚨 IDS/IPS

### Snort (Network IDS)
```bash
# Run in IDS mode
snort -c /etc/snort/snort.conf -A console -i eth0

# Test configuration
snort -T -c /etc/snort/snort.conf

# Key rule for detecting reverse shells
alert tcp $HOME_NET any -> $EXTERNAL_NET any (msg:"Possible Reverse Shell"; flow:established; content:"/bin/bash"; sid:1000001;)
```

### Suricata (Modern Alternative)
```bash
# Run Suricata
suricata -c /etc/suricata/suricata.yaml -i eth0

# Update rules
suricata-update

# Key advantages over Snort:
# - Multi-threaded (faster)
# - Compatible with Snort rules
# - Built-in protocol detection
```

### HIDS (Host-Based)
```bash
# Wazuh agent (monitors file integrity, logs, rootkits)
# OSSEC (predecessor to Wazuh)
# Sysmon (Windows — enhanced logging)
```

---

## 🔀 Network Segmentation

### Architecture
```
Internet
    │
[Firewall / DMZ]
    │
├── DMZ (Web servers, mail servers)
│   └── Only ports 80/443 exposed
│
├── Corporate Network (workstations)
│   └── No direct internet for servers
│
├── Server VLAN (internal services)
│   └── Restricted access from corporate
│
└── Management VLAN (admin access)
    └── Most restricted, jump box only
```

### Key Principles
| Principle | Implementation |
|-----------|---------------|
| Least privilege | Only allow necessary traffic between VLANs |
| Zero Trust | Verify every connection, even internal |
| Micro-segmentation | Isolate critical assets individually |
| Jump boxes | Admin access only through hardened bastion |
| East-West monitoring | IDS between internal segments |

---

## 🔒 Hardening Guides

### Linux Hardening Checklist
```bash
# Disable unnecessary services
systemctl disable <service>
systemctl list-unit-files --state=enabled

# SSH hardening (/etc/ssh/sshd_config)
PermitRootLogin no
PasswordAuthentication no    # Use keys only
MaxAuthTries 3
AllowUsers admin

# File permissions
chmod 600 /etc/shadow
chmod 644 /etc/passwd
find / -perm -4000 -type f   # Audit SUID binaries

# Automatic updates
apt install unattended-upgrades

# Audit logging
apt install auditd
auditctl -w /etc/passwd -p wa -k passwd_changes
auditctl -w /etc/shadow -p wa -k shadow_changes

# Disable IPv6 (if not needed)
echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
```

### Windows Hardening Checklist
```powershell
# Disable SMBv1
Set-SmbServerConfiguration -EnableSMB1Protocol $false

# Enable audit logging
auditpol /set /category:"Logon/Logoff" /success:enable /failure:enable
auditpol /set /category:"Account Logon" /success:enable /failure:enable

# Disable LLMNR (prevents Responder attacks)
# GPO: Computer Config → Admin Templates → Network → DNS Client → Turn off multicast name resolution

# Disable NBT-NS
# Network adapter → IPv4 → Advanced → WINS → Disable NetBIOS over TCP/IP

# Enable Credential Guard (Windows 10/Server 2016+)
# Protects LSASS from Mimikatz

# Restrict PowerShell
Set-ExecutionPolicy Restricted
# Enable PowerShell logging (Script Block + Module logging)

# LAPS (Local Administrator Password Solution)
# Randomizes local admin passwords across domain
```

### CIS Benchmarks
```
- Download from cisecurity.org
- Provides step-by-step hardening for:
  - Windows Server / Desktop
  - Linux (Ubuntu, CentOS, RHEL)
  - Network devices (Cisco, Palo Alto)
  - Cloud (AWS, Azure, GCP)
  - Applications (Apache, Nginx, MySQL)
```

---

## 📡 Network Monitoring

### Key Traffic to Monitor
| Traffic | Why |
|---------|-----|
| DNS queries | C2 domains, tunneling |
| Outbound to unusual ports | Reverse shells |
| SMB between workstations | Lateral movement |
| Large outbound transfers | Exfiltration |
| Beaconing patterns | C2 check-ins |
| Internal port scanning | Reconnaissance |

### Tools
```bash
# Zeek (network metadata)
zeek -i eth0
# Generates conn.log, dns.log, http.log, ssl.log

# NetworkMiner (extract files from PCAP)
# Arkime (full packet capture + search)
# Security Onion (all-in-one monitoring distro)
```

---

## 🔗 Associated Tools
- [Snort](../../TOOLS/snort.md) - Network IDS/IPS
- [TCPdump](../../TOOLS/tcpdump.md) - Packet capture
