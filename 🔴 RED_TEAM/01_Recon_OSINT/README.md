# 🔍 Reconnaissance & OSINT

> **Definition:** The process of gathering information about a target from publicly available sources to identify potential attack vectors. This phase is divided into **Passive Recon** (no direct contact with the target) and **Active Recon** (interaction with the target's infrastructure).

---

## 🛠️ Methodology & Workflow

1.  **Passive OSINT**: Search engines, social media, and third-party databases.
2.  **Infrastructure Mapping**: Identifying IPs, domains, and cloud assets via Shodan/Censys.
3.  **Content Discovery**: Finding hidden directories and unlinked files.

---

## 🔍 Google Dorking (Advanced Search)

Harnessing search engine power to find sensitive data or misconfigured servers.

| Operator | Syntax | Purpose |
| :--- | :--- | :--- |
| **Exact Match** | `"phrase"` | Forces results to contain the exact string. Useful for finding specific error messages. |
| **Site Filter** | `site:domain.com` | Restricts results to a specific domain or TLD (e.g., `site:gov`). |
| **File Type** | `filetype:pdf` | Targets specific formats (pdf, docx, xls, config, log). |
| **In-URL** | `inurl:admin` | Finds strings within the URL path. |
| **In-Title** | `intitle:"index of"` | Often used to find directory listings (sensitive files). |

### 💡 Pro Combo : Finding Exposed Logs
`site:target.com filetype:log "error" "password"`

---

## 🌐 External Infrastructure Discovery

### Shodan
* **Description**: A search engine for Internet-connected devices. It indexes banners (the text returned by services like SSH, HTTP, or FTP).
* **Key Filters**:
    * `hostname:"target.com"`: Finds devices associated with a domain.
    * `port:21`: Scans for open FTP ports.
    * `os:"windows 7"`: Targets specific, potentially vulnerable operating systems.

### Censys
* **Description**: Similar to Shodan but with a strong focus on **SSL/TLS Certificates**.
* **Usage**: Helps identify "shadow IT" by finding certificates issued to a company that point to forgotten subdomains or staging servers.

---

## 🗃️ Vulnerability Databases
Before an attack, research known weaknesses using professional archives:
* **CVE (Common Vulnerabilities and Exposures)**: The standard list of publicly disclosed cybersecurity vulnerabilities. Format: `CVE-YYYY-ID`.
* **Exploit-DB**: A massive archive of public exploits and shellcode maintained by *Offensive Security*. Crucial for finding PoCs (Proof of Concepts).

--- 

## 🔗 Associated Tools
* [DIRB](../../TOOLS/dirb.md) - Web Content Discovery.
* [Whois] - Domain registration data.
* [DNSRecon] - DNS enumeration.
