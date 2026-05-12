# 🛠️ Gobuster

> **Category:** Web Enumeration / Directory Brute-Forcing
> **Purpose:** A high-performance tool written in Go used to discover hidden paths (directories/files) on web servers, subdomains, and virtual host names.

---

## 🚀 Core Modes of Operation
Gobuster uses different "modes" depending on the target.

| Mode | Command | Objective |
| :--- | :--- | :--- |
| **dir** | `gobuster dir` | Classic directory and file brute-forcing. |
| **dns** | `gobuster dns` | Discovering subdomains via a wordlist. |
| **vhost** | `gobuster vhost` | Finding Virtual Hosts (useful for shared hosting). |

---

## 📋 Common Syntax & Flags

### 1. Directory Enumeration (`dir`)
**Standard Command:**
`gobuster dir -u http://10.10.10.10/ -w /usr/share/wordlists/dirb/common.txt`

**Essential Flags:**
*   `-x .php,.txt,.html`: Search for specific **File Extensions**.
*   `-t 50`: Increase **Threads** for higher speed (default is 10).
*   `-k`: **Skip TLS/SSL** verification (use for self-signed certs).
*   `-a "User-Agent String"`: Impersonate a specific browser to bypass simple filters.
*   `-b 404,403`: **Exclude** specific HTTP status codes from the output.

### 2. DNS Subdomain Discovery (`dns`)
`gobuster dns -d target.com -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt`

---

## 🛠️ Authentication & Advanced Access

### Handling Protected Pages
If the site requires a login or a cookie to see certain directories:
*   **Basic Auth**: `-u http://admin:password@10.10.10.10/`
*   **Cookies**: `-c "session=123456789"`
*   **Headers**: `-H "Authorization: Bearer <TOKEN>"`

---

## 🎨 Understanding HTTP Status Codes
Gobuster's output relies on these codes to tell you what it found:

| Code | Meaning | Action |
| :--- | :--- | :--- |
| **200** | **OK** | Content exists and is readable. |
| **301/302** | **Redirect** | Path exists but points elsewhere (follow it). |
| **401** | **Unauthorized** | Requires login (potential brute-force target). |
| **403** | **Forbidden** | Exists but access is denied (check for config leaks). |
| **204** | **No Content** | Path exists but the file is empty. |

---

## 💡 Expert Workflow

### 1. The "Extension Hunter"
Always look for backup or configuration files. Attackers often find sensitive data in `.bak`, `.old`, or `.zip` files.
`gobuster dir -u http://target.com/ -w wordlist.txt -x .php,.bak,.config,.zip,.sql`

### 2. Speed vs. Accuracy
If the server starts returning `429 Too Many Requests`, lower your threads (`-t 5`) and add a delay.

### 3. Recursive Discovery
Gobuster does not automatically "crawl" into found folders. If you find `/admin/`, you must run a **second scan** specifically on `http://target.com/admin/` to find what's inside.

---

## 🔗 Recommended Wordlists (Kali Linux)
*   **/usr/share/wordlists/dirb/common.txt** (Small & Fast)
*   **/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt** (The Standard)
*   **/usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt** (Comprehensive)
