# 🛠️ Hashcat

> **Category:** GPU-Accelerated Password Recovery
> **Key Distinction:** Optimized for raw performance using OpenCL/CUDA (GPU) rather than CPU.

---

## 📋 Core Syntax
**Command Structure:**
`hashcat -m [Mode] -a [Attack_Mode] [Hash_File] [Wordlist/Mask]`

### 1. Attack Modes (`-a`)
* `-a 0`: **Straight** (Standard wordlist)
* `-a 1`: **Combination** (Combines two wordlists)
* `-a 3`: **Brute-force** (Uses Masks)
* `-a 6`: **Hybrid** (Wordlist + Mask)
* `-a 7`: **Hybrid** (Mask + Wordlist)

### 2. Common Hash Modes (`-m`)
* **0**: MD5
* **100**: SHA-1
* **1000**: NTLM (Windows local)
* **1800**: SHA-512 (Linux /etc/shadow)
* **5600**: NetNTLMv2 (Network/Responder)
* **13100**: Kerberoasting (TGS-REP)

---

## 🎭 Mask Attacks (?l, ?u, ?d, ?s)
Masks define the pattern of a password.
| Placeholder | Type |
| :--- | :--- |
| `?l` | Lowercase (a-z) |
| `?u` | Uppercase (A-Z) |
| `?d` | Digits (0-9) |
| `?s` | Special symbols |
| `?a` | All characters |

**Example (Upper + 4 Lower + 1 Digit):**
`hashcat -m 0 -a 3 hashes.txt ?u?l?l?l?l?d`

---

## ⚡ Performance & Controls
* `--force`: Ignores warnings (required in some VMs).
* `-O`: **Optimized Kernels** (Faster, but limits password length).
* `-w 3`: Sets workload to "High."
* `--benchmark`: Runs a speed test.

**In-App Controls:**
* `s`: Status (Temp, Speed, ETA)
* `p`: Pause / `r`: Resume
* `q`: Quit

---

## 💡 Expert One-Liners

**Crack NetNTLMv2 (Responder Capture):**
`hashcat -m 5600 capture.txt /usr/share/wordlists/rockyou.txt`

**Crack NTLM with Rules:**
`hashcat -m 1000 hash.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule`

**Show Already Cracked Passwords:**
`hashcat --show -m 1000 hashes.txt`
