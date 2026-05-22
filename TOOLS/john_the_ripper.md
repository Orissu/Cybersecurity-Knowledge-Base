# 🛠️ John the Ripper (JtR)

> **Category:** CPU-Based Password Cracking
> **When to Use:** Cracking hashes, encrypted files (ZIP, SSH keys, Office docs, KeePass).

---

## 📋 Core Syntax

```bash
john [options] <hash_file>
```

---

## 🔓 Cracking Modes

| Mode | Command | Description |
|------|---------|-------------|
| Single | `john --single hash.txt` | Uses username/GECOS info as base (fastest) |
| Wordlist | `john --wordlist=rockyou.txt hash.txt` | Standard dictionary attack |
| Incremental | `john --incremental hash.txt` | Pure brute-force (all combinations) |
| Rules | `john --wordlist=rockyou.txt --rules hash.txt` | Wordlist + mangling rules |

---

## 📂 The "2John" Ecosystem

Convert encrypted files into crackable hash format:

```bash
# SSH private keys
ssh2john id_rsa > hash.txt

# ZIP archives
zip2john protected.zip > hash.txt

# RAR archives
rar2john protected.rar > hash.txt

# 7z archives
7z2john protected.7z > hash.txt

# PDF files
pdf2john protected.pdf > hash.txt

# MS Office documents
office2john document.docx > hash.txt

# KeePass databases
keepass2john database.kdbx > hash.txt

# GPG/PGP keys
gpg2john private.key > hash.txt

# Encrypted disk images (LUKS)
luks2john disk.img > hash.txt

# /etc/shadow (extract hashes)
unshadow /etc/passwd /etc/shadow > hash.txt

# Kerberos tickets
kirbi2john ticket.kirbi > hash.txt

# Bitcoin wallets
bitcoin2john wallet.dat > hash.txt

# Ansible vault
ansible2john vault.yml > hash.txt

# Mozilla Firefox passwords
mozilla2john key4.db > hash.txt
```

---

## 🎯 Format Specification

```bash
# Auto-detect format (usually works)
john hash.txt

# Specify format explicitly
john --format=raw-md5 hash.txt
john --format=raw-sha256 hash.txt
john --format=nt hash.txt
john --format=bcrypt hash.txt
john --format=sha512crypt hash.txt

# List all supported formats
john --list=formats

# Search for a format
john --list=formats | grep -i "ssh\|zip\|office"
```

### Common Formats
| Format | Hash Type |
|--------|-----------|
| `raw-md5` | Plain MD5 |
| `raw-sha1` | Plain SHA-1 |
| `raw-sha256` | Plain SHA-256 |
| `nt` | Windows NTLM |
| `sha512crypt` | Linux $6$ hashes |
| `bcrypt` | bcrypt ($2a$, $2b$) |
| `krb5tgs` | Kerberoast |
| `krb5asrep` | AS-REP Roast |
| `SSH` | SSH private keys |
| `PKZIP` | ZIP files |
| `Office` | MS Office |
| `KeePass` | KeePass DB |

---

## 📏 Rules (Password Mangling)

### Using Rules
```bash
# Default rules
john --wordlist=rockyou.txt --rules hash.txt

# Specific ruleset
john --wordlist=rockyou.txt --rules=Jumbo hash.txt
john --wordlist=rockyou.txt --rules=KoreLogic hash.txt
john --wordlist=rockyou.txt --rules=All hash.txt
```

### Custom Rules (in john.conf)
```
[List.Rules:MyRules]
Az"[0-9]"          # Append 0-9
Az"[0-9][0-9]"     # Append 00-99
A0"[!@#$]"         # Prepend special char
c                   # Capitalize first letter
c $[0-9]            # Capitalize + append digit
c $[0-9] $[!@#$]   # Capitalize + digit + special
l                   # Lowercase all
u                   # Uppercase all
d                   # Duplicate (wordword)
r                   # Reverse
```

### Using Custom Rules
```bash
john --wordlist=rockyou.txt --rules=MyRules hash.txt
```

---

## ⚡ Useful Options

| Flag | Description |
|------|-------------|
| `--show` | Display cracked passwords |
| `--pot=<file>` | Custom potfile location |
| `--fork=<n>` | Parallel processes (CPU cores) |
| `--session=<name>` | Name session for restore |
| `--restore=<name>` | Resume named session |
| `--max-length=<n>` | Max password length to try |
| `--min-length=<n>` | Min password length to try |
| `--mask=<mask>` | Mask mode (like hashcat) |

---

## 💡 Expert One-Liners

```bash
# === COMMON WORKFLOWS ===

# Crack Linux shadow file
unshadow /etc/passwd /etc/shadow > unshadowed.txt
john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt

# Crack SSH key passphrase
ssh2john id_rsa > ssh_hash.txt
john --wordlist=rockyou.txt ssh_hash.txt

# Crack ZIP file
zip2john secret.zip > zip_hash.txt
john --wordlist=rockyou.txt zip_hash.txt

# Crack KeePass database
keepass2john database.kdbx > keepass_hash.txt
john --wordlist=rockyou.txt keepass_hash.txt

# Crack NTLM hashes
john --format=nt --wordlist=rockyou.txt ntlm_hashes.txt

# Crack Kerberoast tickets
john --format=krb5tgs --wordlist=rockyou.txt tgs_hashes.txt

# === UTILITIES ===

# Show all cracked passwords
john --show hash.txt

# Show cracked in specific format
john --show --format=nt hash.txt

# Check progress (while running, press any key)
# Or from another terminal:
john --status

# List available formats
john --list=formats | wc -l
```

---

## 🔧 Workflow

```
1. Identify what you have (hash, encrypted file, etc.)
2. Convert to john format if needed (*2john tools)
3. Try single mode first (fastest): john --single hash.txt
4. Try wordlist: john --wordlist=rockyou.txt hash.txt
5. Try wordlist + rules: john --wordlist=rockyou.txt --rules hash.txt
6. Check results: john --show hash.txt
```

---

## ⚠️ John vs Hashcat

| Feature | John | Hashcat |
|---------|------|---------|
| Speed | CPU (slower) | GPU (much faster) |
| Format support | More formats (2john tools) | Fewer but common ones |
| Encrypted files | Excellent (SSH, ZIP, Office) | Limited |
| Rules | Good | Better performance |
| Best for | File cracking, varied formats | Raw hash cracking at speed |

**Strategy:** Use John for file-based hashes (SSH, ZIP, KeePass). Use Hashcat for raw hashes (NTLM, NetNTLMv2, Kerberos).

---

## 🔗 Links

- Before John → extract hashes with *2john tools
- Alternative: Hashcat (GPU-accelerated, faster for raw hashes)
- Combine with: CeWL (custom wordlists), Crunch (generate wordlists)
