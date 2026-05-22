# ⌨️ CLI Mastery & Power Tools

> **Scope:** Command-line proficiency, regex, piping, and scripting — the foundation of efficient pentesting.

---

## 🏗️ Shell Fundamentals

### Quoting
| Type | Behavior | Example |
|------|----------|---------|
| Single `' '` | Literal (no expansion) | `echo '$HOME'` → $HOME |
| Double `" "` | Allows variable expansion | `echo "$HOME"` → /home/user |
| Backticks `` ` ` `` | Command substitution | `` echo `whoami` `` → root |
| `$( )` | Command substitution (modern) | `echo $(whoami)` → root |

### Redirection & Piping
```bash
command > file          # Redirect stdout (overwrite)
command >> file         # Redirect stdout (append)
command 2> file         # Redirect stderr
command 2>/dev/null     # Discard errors
command &> file         # Redirect both stdout + stderr
command1 | command2     # Pipe stdout to next command
command | tee file      # Output to screen AND file
```

### Job Control
```bash
command &               # Run in background
Ctrl+Z                  # Suspend current process
bg                      # Resume in background
fg                      # Bring to foreground
jobs                    # List background jobs
nohup command &         # Survives terminal close
disown                  # Detach from terminal
```

---

## 🧩 Regular Expressions (Regex)

### Anchors
| Pattern | Matches |
|---------|---------|
| `^` | Start of line |
| `$` | End of line |
| `\b` | Word boundary |

### Quantifiers
| Pattern | Matches |
|---------|---------|
| `*` | 0 or more |
| `+` | 1 or more |
| `?` | 0 or 1 (optional) |
| `{3}` | Exactly 3 |
| `{2,5}` | Between 2 and 5 |
| `{3,}` | 3 or more |

### Character Classes
| Pattern | Matches |
|---------|---------|
| `.` | Any character (except newline) |
| `\d` | Digit [0-9] |
| `\w` | Word char [a-zA-Z0-9_] |
| `\s` | Whitespace |
| `[abc]` | a, b, or c |
| `[a-z]` | Any lowercase |
| `[^0-9]` | NOT a digit |

### Groups & Alternation
| Pattern | Matches |
|---------|---------|
| `(abc)` | Group "abc" |
| `a|b` | a OR b |
| `(?:abc)` | Non-capturing group |

### Pentest Regex Examples
```bash
# Find IP addresses
grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}' file

# Find email addresses
grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' file

# Find URLs
grep -oE 'https?://[^ ]+' file

# Find hashes (32 hex = MD5/NTLM)
grep -oE '[a-fA-F0-9]{32}' file

# Find private keys
grep -r "BEGIN.*PRIVATE KEY" /

# Find passwords in config files
grep -riE '(password|passwd|pwd|secret|key)\s*[=:]' /var/www/
```

---

## 🔧 Essential CLI Tools for Pentesting

### grep (Search)
```bash
grep "pattern" file              # Basic search
grep -r "password" /var/www/     # Recursive
grep -i "error" file             # Case-insensitive
grep -v "comment"                # Exclude matches
grep -c "pattern" file           # Count matches
grep -l "pattern" *.txt          # List files with match
grep -n "pattern" file           # Show line numbers
grep -E "regex" file             # Extended regex
grep -o "pattern" file           # Only matching part
```

### awk (Column Processing)
```bash
awk '{print $1}' file            # First column
awk -F: '{print $1}' /etc/passwd # Custom delimiter
awk '{print $1, $3}' file        # Multiple columns
awk '$3 > 100' file              # Conditional
awk 'NR==5,NR==10' file          # Lines 5-10
awk '{sum+=$1} END {print sum}'  # Sum column
```

### sed (Find & Replace)
```bash
sed 's/old/new/' file            # Replace first occurrence
sed 's/old/new/g' file           # Replace all
sed -i 's/old/new/g' file       # In-place edit
sed -n '5,10p' file              # Print lines 5-10
sed '/pattern/d' file            # Delete matching lines
sed 's/^/prefix/' file           # Add prefix to lines
```

### sort, uniq, cut, tr
```bash
sort file                        # Sort alphabetically
sort -n file                     # Sort numerically
sort -rn file                    # Reverse numeric sort
uniq file                        # Remove adjacent duplicates
sort | uniq -c                   # Count occurrences
cut -d: -f1 /etc/passwd          # Extract field
cut -d' ' -f1 access.log        # Extract IPs from log
tr 'A-Z' 'a-z'                  # Lowercase
tr -d '\r'                       # Remove Windows line endings
```

### xargs (Build Commands from Input)
```bash
# Run command for each line
cat ips.txt | xargs -I {} nmap -sV {}

# Parallel execution
cat targets.txt | xargs -P 10 -I {} curl -s {}

# Find and delete
find /tmp -name "*.tmp" | xargs rm
```

---

## 📝 One-Liner Recipes (Pentest)

```bash
# Extract unique IPs from log
cat access.log | awk '{print $1}' | sort -u

# Top 10 IPs by request count
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -10

# Find all SUID binaries
find / -perm -4000 -type f 2>/dev/null

# Quick port scan (no nmap)
for port in 22 80 443 445 3389; do (echo > /dev/tcp/TARGET/$port) 2>/dev/null && echo "Port $port open"; done

# Ping sweep
for i in $(seq 1 254); do ping -c 1 -W 1 192.168.1.$i > /dev/null 2>&1 && echo "192.168.1.$i alive"; done

# Extract URLs from HTML
curl -s http://target | grep -oE 'href="[^"]+"' | cut -d'"' -f2

# Base64 decode
echo "encoded_string" | base64 -d

# Generate password list from website
cewl http://target.com -d 2 -m 5 -w wordlist.txt
```

---

## 🔗 See Also
- [Regex Reference](./Regex.md) - Detailed regex patterns
