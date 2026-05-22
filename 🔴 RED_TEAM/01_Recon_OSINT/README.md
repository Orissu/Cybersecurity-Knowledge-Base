# Reconnaissance & OSINT

> **Objective:** Map the target's attack surface before any exploitation. The more you know, the easier the exploitation phase becomes.

---

## Methodology

```
1. Passive Recon (no direct contact with target)
   - OSINT on people, emails, infrastructure
   - Google Dorks, Shodan, DNS records
   
2. Active Recon (direct interaction)
   - Port scanning (Nmap)
   - Service enumeration
   - Directory brute-forcing
```

---

## Passive Reconnaissance

### Google Dorking

| Operator | Purpose | Example |
|----------|---------|---------|
| `site:` | Restrict to domain | `site:target.com` |
| `filetype:` | Find specific files | `filetype:pdf site:target.com` |
| `inurl:` | String in URL | `inurl:admin site:target.com` |
| `intitle:` | String in page title | `intitle:"index of" site:target.com` |
| `intext:` | String in page body | `intext:"password" filetype:log` |
| `cache:` | Cached version | `cache:target.com` |
| `-` | Exclude term | `site:target.com -www` |

#### Useful Dork Combinations
```
# Find exposed config files
site:target.com filetype:env OR filetype:cfg OR filetype:conf

# Find login pages
site:target.com inurl:login OR inurl:admin OR inurl:portal

# Find exposed documents
site:target.com filetype:pdf OR filetype:xlsx OR filetype:docx

# Find directory listings
intitle:"index of" site:target.com

# Find exposed databases
site:target.com filetype:sql OR filetype:db OR filetype:mdb

# Find error messages with info
site:target.com "mysql_connect" OR "ORA-" OR "syntax error"

# Find subdomains
site:*.target.com -www
```

### Shodan (Internet-Connected Devices)

```
# Basic searches
hostname:"target.com"
org:"Target Company"
ssl.cert.subject.cn:"target.com"
net:"192.168.1.0/24"

# Find specific services
hostname:"target.com" port:21
hostname:"target.com" port:3389
hostname:"target.com" product:"Apache"

# Find vulnerabilities
hostname:"target.com" vuln:CVE-2021-44228
"default password" hostname:"target.com"
```

### DNS Reconnaissance

```bash
# WHOIS lookup
whois target.com

# DNS records
dig target.com ANY
dig target.com MX
dig target.com TXT
dig target.com NS
nslookup -type=any target.com

# Zone transfer attempt (often blocked)
dig axfr @ns1.target.com target.com

# Reverse DNS
dig -x 192.168.1.1

# Find mail servers
dig target.com MX +short
```

### Subdomain Enumeration

```bash
# Amass (passive)
amass enum -passive -d target.com -o subdomains.txt

# Subfinder
subfinder -d target.com -o subdomains.txt

# Assetfinder
assetfinder --subs-only target.com

# crt.sh (Certificate Transparency)
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sort -u

# Combine results and resolve
cat subdomains.txt | sort -u | httpx -silent -o alive_subs.txt
```

### Email & People OSINT

```bash
# theHarvester (emails, subdomains, IPs)
theHarvester -d target.com -b google,bing,linkedin -l 500

# Find email format
# Check: hunter.io, phonebook.cz, email-format.com

# LinkedIn enumeration
# Tools: linkedin2username, CrossLinked
```

### Technology Fingerprinting

```bash
# Wappalyzer (browser extension) - passive
# whatweb (CLI)
whatweb target.com

# Identify CMS
whatweb target.com | grep -i "wordpress\|joomla\|drupal"

# Check headers
curl -I https://target.com
```

---

## Active Reconnaissance

### Port Scanning (Nmap)

```bash
# Host discovery
nmap -sn 192.168.1.0/24

# Quick top ports
nmap -T4 --top-ports 1000 -sV target.com

# Full port scan
nmap -T4 -p- --min-rate 5000 target.com

# Detailed scan on found ports
nmap -sC -sV -p 22,80,443,445 -oA scan target.com

# UDP scan (top 20)
nmap -sU --top-ports 20 target.com
```

### Web Content Discovery

```bash
# Gobuster
gobuster dir -u http://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt,html -t 50

# ffuf (faster, more flexible)
ffuf -u http://target.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200,301,302,403

# Find hidden parameters
arjun -u http://target.com/page.php

# Find virtual hosts
ffuf -u http://target.com -H "Host: FUZZ.target.com" -w subdomains.txt -fs <default_size>
```

### Service Enumeration

```bash
# SMB (445)
smbclient -L //target.com -N
enum4linux -a target.com
crackmapexec smb target.com --shares

# LDAP (389)
ldapsearch -x -H ldap://target.com -b "dc=target,dc=com"
nmap --script ldap-search -p 389 target.com

# SNMP (161)
snmpwalk -v2c -c public target.com
onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt target.com

# NFS (2049)
showmount -e target.com

# SMTP (25)
smtp-user-enum -M VRFY -U users.txt -t target.com
```

---

## Recon Workflow Summary

```
Step 1: Passive OSINT
  - Google Dorks, Shodan, crt.sh
  - Subdomain enumeration (amass, subfinder)
  - Email harvesting (theHarvester)
  - Technology identification (whatweb, Wappalyzer)

Step 2: DNS & Infrastructure
  - DNS records, zone transfers
  - WHOIS, IP ranges, ASN
  - Reverse DNS on discovered IPs

Step 3: Active Scanning
  - Nmap (host discovery -> port scan -> service scan)
  - Web content discovery (gobuster, ffuf)
  - Service-specific enumeration (SMB, LDAP, SNMP)

Step 4: Organize & Prioritize
  - Map attack surface
  - Identify low-hanging fruit
  - Plan exploitation phase
```

---

## Key Tools

| Tool | Purpose |
|------|---------|
| Nmap | Port scanning, service detection |
| Amass / Subfinder | Subdomain enumeration |
| theHarvester | Email and subdomain gathering |
| Gobuster / ffuf | Web content discovery |
| Shodan / Censys | Internet device search |
| whatweb | Technology fingerprinting |
| enum4linux | SMB/NetBIOS enumeration |
| DNSrecon | DNS enumeration |

---

## Useful Websites

| Site | Purpose |
|------|---------|
| crt.sh | Certificate transparency logs |
| hunter.io | Email format discovery |
| phonebook.cz | Email/domain/URL search |
| shodan.io | Internet-connected device search |
| censys.io | Certificate and host search |
| dnsdumpster.com | DNS recon and mapping |
| securitytrails.com | Historical DNS data |
| builtwith.com | Technology profiling |
| waybackmachine | Historical website versions |
| exploit-db.com | Google Hacking Database (GHDB) |
