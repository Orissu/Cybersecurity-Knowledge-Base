# 🔨 Hash Cracking & Extraction

> **Objective:** Exploiting weaknesses in password storage and authentication protocols.

---

## 🔓 Windows Authentication Targets

### NetNTLM (v1/v2)
*   **Scenario**: Captured via LLMNR/NBT-NS poisoning (Responder) or SMB relay.
*   **Nature**: Challenge-response. It is **not** the password hash stored on disk; it is a session-specific hash.
*   **Cracking**: Vulnerable to offline dictionary attacks.

### Kerberos (Kerberoasting)
*   **Scenario**: Requesting a Service Ticket (TGS) for any service with an SPN.
*   **Nature**: The TGS is encrypted with the service account's password hash.
*   **Cracking**: Extract the ticket and crack it offline to recover the Service Account password.

---

## 🛠️ Extraction Tools (The "2John" Suite)

| Tool | Target | Syntax |
| :--- | :--- | :--- |
| **zip2john** | Encrypted ZIP | `zip2john protected.zip > zip.hash` |
| **rar2john** | Encrypted RAR | `rar2john protected.rar > rar.hash` |
| **ssh2john** | SSH Private Key | `python3 ssh2john.py id_rsa > rsa.hash` |
| **keepass2john** | KeePass DB | `keepass2john Vault.kdbx > vault.hash` |

---

## 🔗 Associated Tools
* [John the Ripper](../../TOOLS/john_the_ripper.md) - CPU Cracking.
* **Hashcat**: The GPU-equivalent. Use for massive wordlists.
    *   *Note: Hashcat requires the "Mode" (e.g., `-m 1000` for NTLM).*
