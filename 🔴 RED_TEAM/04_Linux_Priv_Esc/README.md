# 🐧 Linux Privilege Escalation: Credential Recovery

> **Objective:** Moving from a low-privilege shell to a higher-privileged user or root by recovering credentials from restricted files, backups, or SSH keys.

---

## 🔑 Credential Extraction Workflow
Often, a low-privilege user has access to files that contain encrypted secrets. The process follows a strict 3-step sequence:

1.  **Discovery**: Identifying sensitive files (e.g., `id_rsa`, `backup.zip`, `config.rar`).
2.  **Extraction**: Using "2John" utilities to pull the crackable hash from the metadata.
3.  **Recovery**: Cracking the hash offline using dictionary or heuristic (Single) modes.

---

## 🛠️ The "2John" Ecosystem
When you encounter protected files, use these specific utilities to prepare your attack:

| Target File | Extraction Command | Format |
| :--- | :--- | :--- |
| **ZIP Archive** | `zip2john archive.zip > hash.txt` | PKZIP / WinZip |
| **RAR Archive** | `rar2john archive.rar > hash.txt` | RAR / RAR5 |
| **SSH Private Key** | `python3 /opt/john/ssh2john.py id_rsa > hash.txt` | OpenSSH / RSA |

> **Note**: Shell redirection (`>`) is mandatory. These tools output the hash to `stdout`, and it must be saved to a file for JtR to process it.

---

## 🎯 Cracking Methodologies

### 1. Single Crack Mode (Heuristic)
*   **Logic**: Uses account metadata (usernames, GECOS fields) to guess passwords. 
*   **Use Case**: Users often use their own names or department names as passwords.
*   **Command**: `john --single --format=[type] hash.txt`

### 2. Wordlist Mode (Dictionary)
*   **Logic**: Tests every entry in a text file against the hash.
*   **Command**: `john --wordlist=/path/to/rockyou.txt --format=[type] hash.txt`

### 3. Custom Mangling Rules
*   **Logic**: Applies transformations to words (e.g., "Mike" -> "Mike123!") to match human behavior.
*   **Theory**: Humans follow predictable patterns (capitalizing the first letter, adding a year at the end).

---

## 🔗 Associated Tools
* [John the Ripper](../../TOOLS/john_the_ripper.md) - The primary cracking engine.
* **Hashcat**: GPU-based alternative for large-scale cracking.
