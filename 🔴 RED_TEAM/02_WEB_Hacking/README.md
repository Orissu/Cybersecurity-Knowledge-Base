# 🕸️ Web Hacking & Exploitation

> **Focus:** Analyzing and exploiting web application vulnerabilities. Understanding the stateless nature of HTTP is the first step to manipulating it.

---

## 🌐 HTTP Fundamentals
To exploit a web app, you must understand how it communicates.

### Request Methods
| Method | Purpose | Security Context |
| :--- | :--- | :--- |
| **GET** | Retrieve data. | Parameters are visible in the URL (exposed in logs). |
| **POST** | Submit data. | Data is in the body. Used for logins and file uploads. |
| **PUT** | Update/Replace. | Can be used for web shell uploads if misconfigured. |
| **DELETE** | Remove record. | Used in API testing to check for IDOR/Access Control flaws. |

---

## 🐚 Web Shells
A **Web Shell** is a script uploaded to a target web server to provide a remote interface for command execution.

### How it works:
1. **Upload**: An attacker finds a file upload vulnerability (LFI or unrestricted upload).
2. **Access**: The attacker navigates to the script's URL (e.g., `http://target.com/uploads/shell.php`).
3. **Execution**: The script uses functions like `system()`, `exec()`, or `passthru()` in PHP to run OS-level commands.

### Popular Shells:
* **p0wny-shell**: A single-file PHP shell with a terminal-like UI.
* **b374k / c99**: Feature-rich shells with file managers and SQL explorers.

---

## 💉 SQL Injection (SQLi)
Automating the detection and exploitation of database vulnerabilities.

### SQLMap Utility
`sqlmap` is the premier tool for identifying and exploiting SQLi.
* **URL-based (GET)**: `sqlmap -u "http://target.com/vuln.php?id=1" --dbs`
* **Request-based (POST)**: If the injection is in a form, intercept the request with a proxy, save it as `request.txt`, and run:
  `sqlmap -r request.txt --batch`

---

## 🔗 Associated Tools
* [Burp Suite](../TOOLS/burpsuite.md) - The "Swiss Army Knife" of web hacking.
* [Hydra](../TOOLS/hydra.md) - High-speed login cracker.
* [Sqlmap](../TOOLS/sqlmap.md) - SQL injection automation.
