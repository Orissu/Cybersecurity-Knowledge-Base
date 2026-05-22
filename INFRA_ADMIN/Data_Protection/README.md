# 💾 Data Protection & Secure Access

> **Scope:** Encryption at rest, secure remote access, and backup strategies.

---

## 🔒 Windows: BitLocker

### How It Works
- Full Disk Encryption (FDE) using AES-128 or AES-256
- Key sealed in TPM chip (released only if boot is untampered)
- Protects data if device is stolen

### BitLocker Commands
```powershell
# Check status
manage-bde -status

# Enable on C: drive
manage-bde -on C: -RecoveryPassword

# Get recovery key
manage-bde -protectors -get C:

# Suspend (for updates)
manage-bde -pause C:
```

### Pentest Relevance
| Scenario | Attack |
|----------|--------|
| BitLocker without TPM PIN | Cold boot attack (freeze RAM) |
| Recovery key in AD | Extract via LDAP if you have creds |
| Suspended BitLocker | Disk accessible without key |
| No BitLocker | Boot from USB, mount disk, extract SAM |

---

## 🐧 Linux: LUKS Encryption

```bash
# Check if disk is encrypted
lsblk
cryptsetup status /dev/mapper/encrypted

# Create encrypted volume
cryptsetup luksFormat /dev/sdb1
cryptsetup luksOpen /dev/sdb1 encrypted
mkfs.ext4 /dev/mapper/encrypted
mount /dev/mapper/encrypted /mnt/secure
```

### Pentest Relevance
- If you have root, LUKS is already unlocked (keys in memory)
- Extract keys with: `dmsetup table --showkeys`
- Volatility can find LUKS keys in memory dumps

---

## 🔑 SSH Key Management

### Generate Keys
```bash
# Ed25519 (recommended — fast, secure, short)
ssh-keygen -t ed25519 -C "user@host"

# RSA (legacy compatibility)
ssh-keygen -t rsa -b 4096 -C "user@host"
```

### Deploy Keys
```bash
# Copy public key to server
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server

# Manual method
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### SSH Hardening (/etc/ssh/sshd_config)
```bash
PermitRootLogin no                    # No direct root login
PasswordAuthentication no             # Keys only
PubkeyAuthentication yes              # Enable key auth
MaxAuthTries 3                        # Limit attempts
AllowUsers admin deploy               # Whitelist users
Protocol 2                            # SSH v2 only
X11Forwarding no                      # Disable X11
```

### SSH Agent (avoid typing passphrase)
```bash
eval $(ssh-agent)
ssh-add ~/.ssh/id_ed25519
```

### Pentest Relevance
| Finding | Action |
|---------|--------|
| id_rsa without passphrase | Direct login to other systems |
| authorized_keys writable | Add your key for persistence |
| SSH agent forwarding | Hijack agent socket for lateral movement |
| Known_hosts file | Reveals other systems user connects to |

---

## 💾 Backup Strategies

### 3-2-1 Rule
```
3 copies of data
2 different storage types
1 offsite copy
```

### Backup Types
| Type | Description | Speed |
|------|-------------|-------|
| Full | Everything | Slow (complete copy) |
| Incremental | Only changes since last backup | Fast |
| Differential | Changes since last full backup | Medium |

### Pentest Relevance
- Backups often contain credentials, configs, databases
- Look for: .bak, .sql, .tar.gz, .zip in accessible shares
- Shadow copies (Windows): `vssadmin list shadows`
- Attackers delete backups before ransomware: `vssadmin delete shadows /all`

---

## 🔗 Associated Tools
- [John the Ripper](../../TOOLS/john_the_ripper.md) - Crack encrypted files
- [Hashcat](../../TOOLS/Hashcat.md) - Crack disk encryption passwords
