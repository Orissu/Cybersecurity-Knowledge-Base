# 🛠️ Hashcat

> **Category:** GPU-Accelerated Password Cracking
> **When to Use:** Cracking captured hashes (NTLM, NetNTLMv2, Kerberos, Linux shadow, web app hashes).

---

## 📋 Core Syntax

```bash
hashcat -m <mode> -a <attack> <hash_file> <wordlist/mask> [options]
```

---

## 🎯 Attack Modes (`-a`)

| Mode | Name | Description |
|------|------|-------------|
| `0` | Straight | Standard wordlist attack |
| `1` | Combination | Combines two wordlists (word1+word2) |
| `3` | Brute-force | Mask-based (character patterns) |
| `6` | Hybrid (WL+Mask) | Wordlist + appended mask |
| `7` | Hybrid (Mask+WL) | Prepended mask + wordlist |

---

## 🔢 Common Hash Modes (`-m`)

### Windows
| Mode | Hash Type | Source |
|------|-----------|--------|
| `1000` | NTLM | SAM dump, secretsdump |
| `5600` | NetNTLMv2 | Responder, relay captures |
| `3000` | LM | Legacy Windows (rare) |

### Linux
| Mode | Hash Type | Source |
|------|-----------|--------|
| `500` | MD5crypt ($1$) | /etc/shadow |
| `1800` | SHA-512crypt ($6$) | /etc/shadow (modern) |
| `7400` | SHA-256crypt ($5$) | /etc/shadow |

### Active Directory / Kerberos
| Mode | Hash Type | Source |
|------|-----------|--------|
| `13100` | Kerberoast (TGS-REP) | Kerberoasting |
| `18200` | AS-REP Roast | AS-REP Roasting |
| `13400` | KeePass | .kdbx files |

### Web / Application
| Mode | Hash Type | Source |
|------|-----------|--------|
| `0` | MD5 | Web apps, databases |
| `100` | SHA-1 | Web apps |
| `1400` | SHA-256 | Modern web apps |
| `3200` | bcrypt | Modern web apps |
| `400` | WordPress (phpass) | WordPress DBs |
| `7900` | Drupal7 | Drupal DBs |

### Network
| Mode | Hash Type | Source |
|------|-----------|--------|
| `2500` | WPA/WPA2 | WiFi captures (.hccapx) |
| `22000` | WPA-PBKDF2-PMKID+EAPOL | WiFi (modern format) |
| `16800` | WPA-PMKID-PBKDF2 | PMKID attack |

### Other
| Mode | Hash Type | Source |
|------|-----------|--------|
| `11300` | Bitcoin wallet | wallet.dat |
| `16600` | Electrum wallet | Electrum |
| `1500` | DES (Unix) | Old systems |

---

## 🎭 Mask Attack Charsets

| Placeholder | Characters |
|-------------|-----------|
| `?l` | abcdefghijklmnopqrstuvwxyz |
| `?u` | ABCDEFGHIJKLMNOPQRSTUVWXYZ |
| `?d` | 0123456789 |
| `?s` | Special chars (space!"#$%&...) |
| `?a` | All of the above combined |
| `?b` | 0x00-0xff (binary) |

### Custom Charsets
```bash
# Define custom charset
hashcat -m 1000 -a 3 hash.txt -1 ?l?d ?1?1?1?1?1?1
# ?1 = lowercase + digits

# Multiple custom charsets
hashcat -m 1000 -a 3 hash.txt -1 ?l?u -2 ?d?s ?1?1?1?1?2?2
```

### Mask Examples
```bash
# 8 char, starts with uppercase, ends with digit
?u?l?l?l?l?l?l?d

# 6-8 chars all lowercase (use --increment)
?l?l?l?l?l?l?l?l --increment --increment-min 6

# Common pattern: Word + 2 digits + special
?u?l?l?l?l?d?d?s
```

---

## 📏 Rules (Password Mangling)

Rules transform wordlist entries to match common password patterns.

### Built-in Rule Files
```bash
/usr/share/hashcat/rules/best64.rule          # Fast, good coverage
/usr/share/hashcat/rules/rockyou-30000.rule   # Aggressive
/usr/share/hashcat/rules/d3ad0ne.rule         # Large ruleset
/usr/share/hashcat/rules/dive.rule            # Very large
/usr/share/hashcat/rules/OneRuleToRuleThemAll.rule  # Community favorite
```

### Using Rules
```bash
hashcat -m 1000 hash.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

### Common Rule Functions
| Function | Description | Example |
|----------|-------------|---------|
| `:` | Do nothing | password → password |
| `l` | Lowercase all | PASSWORD → password |
| `u` | Uppercase all | password → PASSWORD |
| `c` | Capitalize first | password → Password |
| `t` | Toggle case | password → PASSWORD |
| `$X` | Append char X | password → password1 |
| `^X` | Prepend char X | password → 1password |
| `d` | Duplicate word | password → passwordpassword |
| `r` | Reverse | password → drowssap |

---

## ⚡ Performance Options

| Flag | Description |
|------|-------------|
| `--force` | Ignore warnings (needed in VMs) |
| `-O` | Optimized kernels (faster, limits length to 32) |
| `-w 3` | Workload high (1=low, 2=default, 3=high, 4=nightmare) |
| `--benchmark` | Speed test all hash modes |
| `-D 1,2` | Device types (1=CPU, 2=GPU) |
| `--session=<name>` | Name session for restore |
| `--restore` | Resume interrupted session |

---

## 💡 Expert One-Liners

```bash
# === WINDOWS ===

# Crack NTLM hashes (SAM dump)
hashcat -m 1000 ntlm_hashes.txt /usr/share/wordlists/rockyou.txt -r best64.rule

# Crack NetNTLMv2 (Responder capture)
hashcat -m 5600 responder_hashes.txt /usr/share/wordlists/rockyou.txt

# === ACTIVE DIRECTORY ===

# Crack Kerberoast tickets
hashcat -m 13100 tgs_hashes.txt /usr/share/wordlists/rockyou.txt -r best64.rule

# Crack AS-REP Roast
hashcat -m 18200 asrep_hashes.txt /usr/share/wordlists/rockyou.txt

# === LINUX ===

# Crack SHA-512 shadow hashes
hashcat -m 1800 shadow_hashes.txt /usr/share/wordlists/rockyou.txt

# === WEB ===

# Crack MD5 hashes
hashcat -m 0 md5_hashes.txt /usr/share/wordlists/rockyou.txt -r best64.rule

# Crack bcrypt
hashcat -m 3200 bcrypt_hashes.txt /usr/share/wordlists/rockyou.txt

# === WIFI ===

# Crack WPA2 handshake
hashcat -m 22000 capture.hc22000 /usr/share/wordlists/rockyou.txt

# === HYBRID ATTACKS ===

# Wordlist + 4 digits appended
hashcat -m 1000 hash.txt rockyou.txt -a 6 ?d?d?d?d

# 2 digits prepended + wordlist
hashcat -m 1000 hash.txt rockyou.txt -a 7 ?d?d

# === UTILITIES ===

# Show cracked passwords
hashcat -m 1000 hash.txt --show

# Identify hash type
hashcat --identify hash.txt

# List all hash modes
hashcat --help | grep -i "ntlm\|sha\|md5"
```

---

## 🔧 Workflow

```
1. Identify hash type (hashid, hashcat --identify, or haiti)
2. Select correct -m mode
3. Start with wordlist + rules: -a 0 rockyou.txt -r best64.rule
4. If no results → try larger wordlists or combination attacks
5. If still nothing → mask attack based on password policy
6. Check results: hashcat --show
```

---

## ⚠️ Common Mistakes

| Mistake | Fix |
|---------|-----|
| Wrong hash mode | Use `hashcat --identify` or `hashid` |
| Hash format wrong | Check for extra whitespace, correct format |
| GPU not detected | Install proper drivers, use `--force` in VM |
| Session interrupted | Use `--restore` to continue |
| Too slow on bcrypt | bcrypt is intentionally slow — use smaller wordlists |

---

## 🔗 Links

- Before Hashcat → capture hashes (Responder, secretsdump, SAM dump)
- Alternative: John the Ripper (CPU-based, more format support)
- Combine with: CeWL (custom wordlist from target website)
- Hash identification: hashid, haiti, hashcat --identify
