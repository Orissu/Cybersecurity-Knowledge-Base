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
*   **/usr/share/wordlists/dirb/common.txt** (Small & Fast — ~4600 entries)
*   **/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt** (The Standard — ~220k)
*   **/usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt** (Comprehensive)
*   **/usr/share/seclists/Discovery/Web-Content/big.txt** (Large — ~20k)
*   **/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt** (DNS enum)

---

## 💡 Expert One-Liners

```bash
# Standard directory scan with extensions
gobuster dir -u http://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt,html,bak -t 50

# Subdomain enumeration
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 50

# VHost discovery
gobuster vhost -u http://target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# With authentication cookie
gobuster dir -u http://target.com/admin -w wordlist.txt -c "session=abc123" -x php

# Ignore SSL errors + custom User-Agent
gobuster dir -u https://target.com -w wordlist.txt -k -a "Mozilla/5.0" -t 30

# Filter by response size (hide 404 custom pages)
gobuster dir -u http://target.com -w wordlist.txt --exclude-length 1234
```

---

## ⚠️ Common Mistakes

| Mistake | Fix |
|---------|-----|
| Too many 403s flooding output | Use `-b 403` to exclude |
| Custom 404 pages showing as 200 | Use `--exclude-length` to filter by size |
| Server rate-limiting (429) | Lower threads `-t 5`, add delay |
| Missing results | Try different wordlists, add `-x` extensions |
| HTTPS errors | Add `-k` to skip TLS verification |

---

## 🔗 Alternatives Comparison

| Tool | Speed | Features | Best For |
|------|-------|----------|----------|
| Gobuster | Fast | Simple, reliable | Quick directory brute-force |
| ffuf | Fastest | Flexible, filters, fuzzing | Advanced fuzzing (params, headers) |
| feroxbuster | Fast | Auto-recursive, smart | Deep recursive discovery |
| dirb | Slow | Simple | Basic scans, legacy |
| dirsearch | Medium | Python, good defaults | Easy to use |
