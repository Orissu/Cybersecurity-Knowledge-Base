# 🔍 Threat Hunting & OSINT (Open Source Intelligence)

> **Objective:** Identifying exposed assets, leaked credentials, and infrastructure vulnerabilities using publicly available data sources.

---

## 🌐 Google Dorking (Advanced Search)
Search engines index more than just websites; they index misconfigured directories, log files, and sensitive documents.

| Operator | Function | Example |
| :--- | :--- | :--- |
| `"text"` | **Exact Match** | `"index of /admin"` |
| `site:` | **Domain Filter** | `site:github.com "company_name"` |
| `filetype:` | **Specific Extension** | `filetype:env DB_PASSWORD` |
| `-` | **Exclusion** | `site:microsoft.com -www` (Finds subdomains) |
| `intitle:` | **Page Title** | `intitle:"Dashboard" admin` |

---

## 📡 Infrastructure Search Engines
While Google indexes web pages, these engines index **IP addresses, ports, and certificates.**

### 1. Shodan (The Search Engine for IoT)
*   **Purpose**: Scans the entire internet for banners (textual descriptions of services).
*   **Key Filters**: `port:21`, `country:"US"`, `product:"Apache"`.
*   **Use Case**: Finding industrial control systems, open webcams, or unpatched routers.


### 2. Censys
*   **Purpose**: Provides a data-driven view of every host and network. 
*   **Focus**: Heavy emphasis on **SSL/TLS Certificates**.
*   **Use Case**: Identifying all subdomains of a target by analyzing the "Common Name" (CN) field in their certificates.

---

## 🛡️ Malware & Breach Analysis

### 1. VirusTotal
*   **Mechanism**: An aggregator that runs a file or URL through **70+ antivirus engines** and blacklists.
*   **Value**: It provides "Crowdsourced Intelligence." If one engine detects a file as "Emotet," you get instant verification.
*   **Operational Note**: Be careful! Uploading a sensitive document to VirusTotal makes it available to all premium subscribers (including adversaries).

### 2. Have I Been Pwned (HIBP)
*   **Purpose**: Tracks historical data breaches and "pastes" (text dumps).
*   **Use Case**: Enter a corporate email to see if it was leaked in a 3rd party breach (e.g., LinkedIn or Adobe).
*   **Red Team Context**: Used to find "base" passwords for users to use in password spraying attacks.

---

## 💡 Expert Tip: Pivot Tables for Threat Hunting
When using **Censys** or **Shodan**, don't just look for IP addresses. Pivot on the **SSL Fingerprint** or the **JARM hash**. If an attacker uses the same custom C2 (Command & Control) server configuration, they will have the same JARM hash across all their infrastructure, allowing you to find their entire hidden network.
