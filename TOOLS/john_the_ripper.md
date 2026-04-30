# 🛠️ John the Ripper (JtR)

> **Category:** Offline Hash Cracking

## 🔓 Core Cracking Modes
1.  **Single Crack**: `john --single <file>` - Uses username/GECOS info (very fast).
2.  **Wordlist**: `john --wordlist=rockyou.txt <file>` - The standard dictionary attack.
3.  **Incremental**: `john --incremental <file>` - Brute-forces all possible combinations.

## 📂 The "2John" Ecosystem
Convert encrypted files into crackable hashes first:
*   `ssh2john id_rsa > hash.txt`: Crack SSH key passphrases.
*   `zip2john files.zip > hash.txt`: Crack encrypted ZIP archives.
*   `office2john doc.docx > hash.txt`: Crack protected MS Office files.

## 🧠 Custom Rule Mangling
Rules transform dictionary words (e.g., `apple` -> `Apple123!`).
*   `--rules`: Enables default mangling.
*   **Custom Syntax** (in `john.conf`):
    *   `Az"[0-9]"`: Appends 0-9 to every word.
    *   `c`: Capitalize first letter.
    *   `$!`: Append `!` to the end.

## 💡 Expert Tip
To see what you have already cracked without re-running the tool:
`john --show <hash_file>`
Check your progress on a long crack by pressing the **Spacebar** or **Enter** while the process is running.
