# 💾 Data Protection & Practical Encryption

## 🛡️ Windows: BitLocker & TPM
**BitLocker** provides Full Disk Encryption (FDE) to protect data if a device is lost or stolen.

### The Role of the TPM (Trusted Platform Module)
The TPM is a hardware chip that acts as a secure vault:
*   **Sealed Keys**: The encryption key is "sealed" to the TPM. It only releases the key if the boot files (BIOS, MBR) are untampered.
*   **Anti-Hammering**: Protects against brute-force attacks on the Windows PIN.
*   **Measured Boot**: Records the state of each boot component to detect rootkits.

---

## 🐧 Linux: SSH & Secure Transfers

### SSH Key-Based Authentication
Replacing passwords with Asymmetric Keys is the #1 hardening step for Linux servers.

| Step | Command |
| :--- | :--- |
| **Generate Key** | `ssh-keygen -t ed25519 -C "admin@domain"` |
| **Deploy Key** | `ssh-copy-id -i ~/.ssh/id_ed25519.pub user@remote_ip` |
| **Secure Copy** | `scp -P 2222 source_file user@remote_ip:/path` |

### Hardening `sshd_config`
To enforce cryptographic standards, modify `/etc/ssh/sshd_config`:
*   `PasswordAuthentication no`: Force keys only.
*   `PermitRootLogin no`: Prevent direct root access.
*   `PubkeyAuthentication yes`: Enable key usage.

---

## 💡 Expert Tip: Entropy
Cryptographic strength relies on **randomness (entropy)**. On Linux, check your available entropy with:
`cat /proc/sys/kernel/random/entropy_avail`
*If below 200, cryptographic operations may hang or become predictable.*
