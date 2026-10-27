# 🔍 Reconnaissance & OSINT

> **Objective:** Gather maximum information about a target before touching it. The more you know, the smaller your attack surface to focus on.

---

## 📋 Methodology

```
Phase 1: Passive Recon (no direct contact with target)
  ├── OSINT on people (employees, emails, social media)
  ├── Domain & DNS enumeration
  ├── Technology fingerprinting
  ├── Google Dorking
  └── Data breach searches

Phase 2: Active Recon (direct interaction)
  ├── Port scanning (Nmap)
  ├── Service enumeration
  ├── Web content discovery
  └── Vulnerability scanning
```

---

## 🔍 Google Dorking

### Essential Operators
| Operator | Purpose | Example |
|----------|---------|---------|
| `site:` | Restrict to domain | `site:target.com` |
| `filetype:` | Find specific files | `filetype:pdf site:target.com` |
| `inurl:` | String in URL | `inurl:admin site:target.com` |
| `intitle:` | String in page title | `intitle:"index of" site:target.com` |
| `intext:` | String in page body | `intext:"password" site:target.com` |
| `cache:` | Google cached version | `cache:target.com` |
| `-` | Exclude term | `site:target.com -www` |

### Pentest Dorks Collection
```
# Find exposed directories
intitle:"index of" site:target.com

# Find configuration files
filetype:env site:target.com
filetype:xml site:target.com
filetype:conf site:target.com
filetype:cfg site:target.com

# Find backup files
filetype:bak site:target.com
filetype:sql site:target.com
filetype:log site:target.com

# Find login pages
inurl:login site:target.com
inurl:admin site:target.com
inurl:portal site:target.com

# Find exposed documents
filetype:pdf site:target.com confidential
filetype:xlsx site:target.com password
filetype:docx site:target.com internal

# Find subdomains
site:*.target.com -www

# Find exposed credentials
intext:"password" filetype:log site:target.com
inurl:"/wp-content/debug.log" site:target.com
```

---

## 🌐 Subdomain Enumeration

### Passive Methods
```bash
# Subfinder (fast, passive)
subfinder -d target.com -o subdomains.txt

# Amass (comprehensive, passive)
amass enum -passive -d target.com -o amass_results.txt

# crt.sh (Certificate Transparency logs)
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sort -u

# SecurityTrails API
curl "https://api.securitytrails.com/v1/domain/target.com/subdomains" -H "APIKEY: YOUR_KEY"

# Wayback Machine (historical URLs)
curl -s "http://web.archive.org/cdx/search/cdx?url=*.target.com/*&output=text&fl=original" | sort -u
```

### Active Methods
```bash
# DNS brute-force with gobuster
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 50

# Amass active
amass enum -active -d target.com -brute

# DNSRecon
dnsrecon -d target.com -t brt -D /usr/share/wordlists/dnsmap.txt
```

### DNS Zone Transfer (misconfiguration)
```bash
# Check if zone transfer is allowed
dig axfr target.com @ns1.target.com
host -t axfr target.com ns1.target.com
dnsrecon -d target.com -t axfr
```

---

## 🏢 Infrastructure Discovery

### Shodan
```
# Search by hostname
hostname:"target.com"

# Search by IP range
net:"192.168.1.0/24"

# Find specific services
hostname:"target.com" port:22
hostname:"target.com" product:"Apache"

# Find vulnerable services
hostname:"target.com" vuln:CVE-2021-44228

# Interesting filters
http.title:"Dashboard"
http.favicon.hash:<hash>
ssl.cert.subject.cn:"target.com"
```

### Censys
```
# Find certificates (reveals subdomains)
parsed.names: target.com

# Find services
ip: 1.2.3.4 AND protocols: "443/https"
```

### Other Tools
```bash
# theHarvester (emails, subdomains, IPs)
theHarvester -d target.com -b all -l 500

# Maltego (visual OSINT mapping)
# GUI tool - map relationships between entities

# Recon-ng (modular recon framework)
recon-ng
marketplace install all
modules load recon/domains-hosts/hackertarget
options set SOURCE target.com
run
```

---

## 👤 OSINT on People

### Email Discovery
```bash
# theHarvester
theHarvester -d target.com -b google,linkedin -l 200

# hunter.io (web)
# https://hunter.io/domain-search - find email patterns

# LinkedIn (manual)
# Search company employees, note naming conventions
# firstname.lastname@target.com is most common
```

### Username OSINT
```bash
# Sherlock (find accounts across platforms)
sherlock username

# namechk.com / whatsmyname.app (web-based)
```

### Data Breach Searches
```
# dehashed.com - search by email/domain
# haveibeenpwned.com - check if email is in breaches
# intelx.io - intelligence search engine
```

---

## 🔧 Technology Fingerprinting

### Web Technologies
```bash
# Wappalyzer (browser extension - passive)
# Identifies CMS, frameworks, languages, servers

# whatweb (CLI)
whatweb target.com
whatweb -a 3 target.com  # aggressive mode

# Builtwith (web)
# https://builtwith.com/target.com
```

### CMS Detection
```bash
# WordPress
wpscan --url http://target.com --enumerate u,p,t

# Joomla
joomscan --url http://target.com

# Drupal
droopescan scan drupal -u http://target.com
```

---

## 📡 Active Reconnaissance

### Port Scanning Strategy
```bash
# Step 1: Quick discovery
nmap -sn 10.10.10.0/24

# Step 2: Fast top ports
nmap -T4 --top-ports 1000 -sV <target>

# Step 3: Full port scan
nmap -T4 -p- --min-rate 5000 <target>

# Step 4: Detailed scan on found ports
nmap -sC -sV -p <ports> -oA detailed <target>
```

### Web Content Discovery
```bash
# ffuf (fastest)
ffuf -u http://target.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 50

# With extensions
ffuf -u http://target.com/FUZZ -w wordlist.txt -e .php,.txt,.html,.bak -t 50

# Gobuster
gobuster dir -u http://target.com -w wordlist.txt -x php,txt,bak -t 50

# Feroxbuster (recursive)
feroxbuster -u http://target.com -w wordlist.txt -x php,txt -t 50
```

### Vulnerability Scanning
```bash
# Nikto (web vulnerabilities)
nikto -h http://target.com

# Nuclei (template-based scanner)
nuclei -u http://target.com -t cves/ -t vulnerabilities/

# Nmap vuln scripts
nmap --script vuln -sV -p 80,443 <target>
```

---

## 💡 Recon Workflow (Step by Step)

```
1. Passive OSINT
   └── Google Dorks → subdomains → emails → tech stack

2. Subdomain Enumeration
   └── subfinder + amass + crt.sh → compile unique list

3. Port Scanning
   └── Nmap on all discovered hosts/subdomains

4. Service Enumeration
   └── Detailed scan on open ports

5. Web Content Discovery
   └── ffuf/gobuster on all web services

6. Vulnerability Identification
   └── Nuclei + manual testing on interesting findings

7. Compile Attack Surface
   └── Document all entry points for exploitation phase
```

---

## 🔗 Key Resources

| Resource | URL | Purpose |
|----------|-----|---------|
| crt.sh | crt.sh | Certificate transparency |
| Shodan | shodan.io | Internet-connected devices |
| Censys | search.censys.io | Certificate & host search |
| Hunter.io | hunter.io | Email discovery |
| SecurityTrails | securitytrails.com | DNS history |
| BuiltWith | builtwith.com | Technology profiling |
| Wayback Machine | web.archive.org | Historical snapshots |
| DNSDumpster | dnsdumpster.com | DNS recon |

---

## 🔗 Associated Tools
- [Nmap](../../TOOLS/nmap.md) - Port scanning & service enumeration
- [Gobuster](../../TOOLS/gobuster.md) - Directory & subdomain brute-force
- [DIRB](../../TOOLS/dirb.md) - Web content discovery
