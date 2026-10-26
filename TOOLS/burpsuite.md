# 🛠️ Burp Suite

> **Category:** Web Application Security Testing / Intercepting Proxy
> **When to Use:** ANY web application pentest. It's your primary tool for web hacking.

---

## 📋 Module Overview

| Module | Purpose | Key Use Case |
|--------|---------|--------------|
| **Proxy** | Intercept & modify HTTP/S traffic | Tampering with requests in real-time |
| **Repeater** | Manual request re-sending & editing | Testing payloads iteratively |
| **Intruder** | Automated payload spraying | Brute-force, fuzzing, parameter enumeration |
| **Decoder** | Encode/decode data | Base64, URL encoding, hex conversion |
| **Comparer** | Byte-level diff between responses | Spotting subtle differences (blind SQLi) |
| **Sequencer** | Token randomness analysis | Testing session token predictability |
| **Logger** | Full HTTP history | Reviewing all traffic passively |
| **Collaborator** | Out-of-band interaction server | Detecting blind SSRF, XXE, RCE |

---

## 🔧 Initial Setup

### Browser Configuration
1. Set browser proxy to `127.0.0.1:8080`
2. Install Burp's CA certificate (visit `http://burp` while proxy is active)
3. Use FoxyProxy extension for easy proxy toggling

### Scope Configuration
- Target → Scope → Add target URL
- Only intercept/log in-scope traffic
- Reduces noise from third-party requests

### Essential Settings
```
Proxy → Options → Intercept Client Requests:
  - Enable "Intercept requests based on scope"

Proxy → Options → Response Modification:
  - Enable "Unhide hidden form fields"
  - Enable "Remove JavaScript form validation"
```

---

## 🎯 Proxy Module

### Intercept Controls
| Action | Purpose |
|--------|---------|
| Forward | Send request to server |
| Drop | Block the request |
| Send to Repeater | Manual testing |
| Send to Intruder | Automated testing |

### Match & Replace Rules
Automatically modify requests/responses:
```
Match: Connection: close
Replace: Connection: keep-alive

Match: X-Forwarded-For:.*
Replace: X-Forwarded-For: 127.0.0.1
```

### Key Tips
- **HTTP History** logs everything even when not intercepting
- Right-click any request → "Send to..." for further testing
- Use "Copy as curl command" for scripting

---

## 🔁 Repeater Module

### Workflow
1. Capture request in Proxy
2. Send to Repeater (Ctrl+R)
3. Modify parameters manually
4. Click "Send" and analyze response
5. Iterate with different payloads

### Use Cases
- Testing SQLi payloads one by one
- Modifying cookies/tokens
- Changing HTTP methods (GET → POST → PUT)
- Adding/removing headers
- Testing IDOR by changing IDs

---

## 💣 Intruder Module

### Attack Types

| Type | Description | Use Case |
|------|-------------|----------|
| **Sniper** | One payload position at a time | Testing single parameter |
| **Battering Ram** | Same payload in all positions | Same value everywhere |
| **Pitchfork** | Different payload per position (parallel) | Username:password pairs |
| **Cluster Bomb** | All combinations of all payloads | Full brute-force |

### Setting Up an Attack
1. Send request to Intruder
2. Mark payload positions with `§` markers: `username=§admin§&password=§test§`
3. Choose attack type
4. Load payload lists
5. Start attack

### Payload Types
| Type | Description |
|------|-------------|
| Simple list | Custom wordlist |
| Numbers | Sequential/random numbers |
| Dates | Date formats |
| Brute forcer | Character set combinations |
| Null payloads | Empty (for timing attacks) |
| Recursive grep | Extract from previous response |

### Analyzing Results
- Sort by **Status Code** (200 vs 302 vs 403)
- Sort by **Length** (different length = different response = potential hit)
- Use **Grep - Match** to flag responses containing specific strings
- Use **Grep - Extract** to pull data from responses

---

## 🔐 Authentication Testing

### Session Token Analysis (Sequencer)
1. Capture a request that returns a session token
2. Send to Sequencer
3. Collect 10,000+ tokens
4. Analyze randomness (entropy)
5. Low entropy = predictable tokens = session hijacking

### Cookie Manipulation
```
# Test privilege escalation
Cookie: role=user → role=admin
Cookie: isAdmin=false → isAdmin=true

# Test IDOR
Cookie: userId=1234 → userId=1235
```

---

## 🌐 Collaborator (Pro Feature)

### What It Does
Provides a unique external server that detects out-of-band interactions:
- DNS lookups
- HTTP requests
- SMTP connections

### Use Cases
```
# Blind SSRF
url=http://BURP-COLLABORATOR-SUBDOMAIN

# Blind XXE
<!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN">

# Blind RCE
; nslookup BURP-COLLABORATOR-SUBDOMAIN

# Blind SQLi (DNS exfiltration)
'; exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'--
```

---

## 🧩 Useful Extensions (BApp Store)

| Extension | Purpose |
|-----------|---------|
| **Autorize** | Automated authorization testing (IDOR/privilege escalation) |
| **Logger++** | Enhanced logging with filters |
| **Param Miner** | Discover hidden parameters |
| **JWT Editor** | Decode/edit/attack JWT tokens |
| **Hackvertor** | Advanced encoding/decoding |
| **Active Scan++** | Enhanced active scanning |
| **Turbo Intruder** | Faster Intruder (Python scripting) |
| **Upload Scanner** | Test file upload vulnerabilities |
| **InQL** | GraphQL introspection & testing |
| **IP Rotate** | Rotate source IP via AWS API Gateway |

---

## ⌨️ Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| Ctrl+R | Send to Repeater |
| Ctrl+I | Send to Intruder |
| Ctrl+Shift+R | Go to Repeater |
| Ctrl+Shift+I | Go to Intruder |
| Ctrl+F | Forward intercepted request |
| Ctrl+D | Drop intercepted request |
| Ctrl+U | URL-encode selected text |
| Ctrl+Shift+U | URL-decode selected text |

---

## 💡 Expert Workflow for Web Pentesting

```
1. Configure scope (Target → Scope)
2. Browse the application manually with proxy ON (build sitemap)
3. Review HTTP History for interesting endpoints
4. Identify input points (parameters, headers, cookies)
5. Send interesting requests to Repeater
6. Test manually for:
   - SQLi (add ' or " and check errors)
   - XSS (inject <script>alert(1)</script>)
   - IDOR (change IDs)
   - Auth bypass (remove tokens, change roles)
7. Use Intruder for automated testing where needed
8. Check Collaborator for blind vulnerabilities
```

---

## ⚠️ Common Mistakes

| Mistake | Fix |
|---------|-----|
| HTTPS not working | Install Burp CA certificate in browser |
| Too much noise in history | Configure scope properly |
| Intruder too slow (Community) | Use Turbo Intruder extension |
| Missing requests | Check "Intercept is off" isn't filtering |
| Can't see response body | Check Content-Type rendering |


---

## 🔗 Links

- Combine with: Browser DevTools (for JS analysis), ffuf (for faster fuzzing)
- After Burp → exploit found vulns with sqlmap, custom scripts
- Practice: PortSwigger Web Security Academy (free labs built for Burp)
