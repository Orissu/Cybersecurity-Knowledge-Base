# 🔐 Cryptography

> **Scope:** Understanding encryption, hashing, and PKI — essential for attacking and defending data protection.

---

## 🏛️ CIA Triad + Non-Repudiation

| Pillar | Goal | Mechanism |
|--------|------|-----------|
| Confidentiality | Only authorized can read | Encryption (AES, RSA) |
| Integrity | Data hasn't been altered | Hashing (SHA-256) |
| Availability | Systems are accessible | Redundancy, backups |
| Non-Repudiation | Cannot deny an action | Digital signatures |

---

## ⚙️ Symmetric Encryption (Same Key)

Both parties share the same secret key.

| Algorithm | Key Size | Speed | Use Case |
|-----------|----------|-------|----------|
| AES-128 | 128-bit | Fast | General encryption |
| AES-256 | 256-bit | Fast | High-security (military, gov) |
| ChaCha20 | 256-bit | Very fast | Mobile, TLS 1.3 |
| 3DES | 168-bit | Slow | Legacy (avoid) |
| RC4 | Variable | Fast | Broken (avoid) |

**Pentest relevance:** If you find the key, you decrypt everything. Look for hardcoded keys in source code, config files, environment variables.

---

## 🔑 Asymmetric Encryption (Key Pair)

Public key encrypts, private key decrypts (or vice versa for signing).

| Algorithm | Based On | Use Case |
|-----------|----------|----------|
| RSA | Prime factorization | SSH, TLS, signing |
| ECC | Elliptic curves | Modern TLS, smaller keys |
| Diffie-Hellman | Discrete logarithm | Key exchange |
| Ed25519 | Elliptic curves | SSH keys (recommended) |

**Pentest relevance:** Steal private keys (~/.ssh/id_rsa), weak key generation, padding oracle attacks.

---

## #️⃣ Hashing (One-Way)

| Algorithm | Output | Status | Use |
|-----------|--------|--------|-----|
| MD5 | 128-bit (32 hex) | Broken | File verification (not security) |
| SHA-1 | 160-bit (40 hex) | Broken | Legacy, avoid |
| SHA-256 | 256-bit (64 hex) | Secure | General purpose |
| SHA-512 | 512-bit (128 hex) | Secure | Linux /etc/shadow ($6$) |
| bcrypt | Variable | Secure | Password storage (slow by design) |
| Argon2 | Variable | Best | Modern password hashing |
| NTLM | 128-bit | Weak | Windows passwords |

### Identifying Hashes
```bash
# By format
$1$... = MD5crypt (Linux)
$5$... = SHA-256crypt (Linux)
$6$... = SHA-512crypt (Linux)
$2a$... = bcrypt
32 hex chars = MD5 or NTLM
40 hex chars = SHA-1
64 hex chars = SHA-256

# Tools
hashid 'hash_value'
hashcat --identify hash.txt
hash-identifier
```

---

## 🏗️ PKI (Public Key Infrastructure)

### Certificate Chain of Trust
```
Root CA (self-signed, offline)
└── Intermediate CA (signs end-entity certs)
    └── End-Entity Certificate (your website)
```

### TLS/SSL Handshake (simplified)
```
1. Client Hello (supported ciphers, TLS version)
2. Server Hello (chosen cipher, server certificate)
3. Client verifies certificate chain
4. Key exchange (Diffie-Hellman or RSA)
5. Session key established (symmetric encryption begins)
6. Encrypted communication
```

### Certificate Attacks
| Attack | Description |
|--------|-------------|
| Self-signed cert | No CA verification (MITM possible) |
| Expired cert | May be ignored by users |
| Wildcard abuse | *.domain.com covers all subdomains |
| ADCS exploitation | Misconfigured certificate templates (ESC1-8) |

---

## 🔓 Crypto Attacks (Pentest Relevant)

| Attack | Target | Description |
|--------|--------|-------------|
| Brute-force | Weak keys/passwords | Try all combinations |
| Dictionary | Password hashes | Wordlist-based cracking |
| Rainbow tables | Unsalted hashes | Pre-computed hash lookup |
| Padding Oracle | CBC mode encryption | Decrypt without key |
| Downgrade | TLS negotiation | Force weak cipher |
| POODLE | SSLv3 | Exploit CBC padding |
| BEAST | TLS 1.0 | CBC IV prediction |
| Heartbleed | OpenSSL | Memory leak (CVE-2014-0160) |
| Key reuse | Stream ciphers | XOR known plaintext |

---

## 💡 Pentest Crypto Checklist

```
1. Check TLS version (TLS 1.0/1.1 = vulnerable)
   nmap --script ssl-enum-ciphers -p 443 target

2. Check for weak ciphers
   testssl.sh target.com

3. Look for hardcoded keys/secrets
   grep -r "BEGIN RSA\|password\|secret\|key" /var/www/

4. Check certificate validity
   openssl s_client -connect target:443

5. Test for Heartbleed
   nmap --script ssl-heartbleed -p 443 target
```

---

## 🔗 Associated Tools
- [Hashcat](../../TOOLS/Hashcat.md) - Hash cracking
- [John the Ripper](../../TOOLS/john_the_ripper.md) - Hash cracking
