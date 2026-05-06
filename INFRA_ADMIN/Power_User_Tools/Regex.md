# 🛠️ Regex & CLI Syntax Cheat Sheet

## 📋 Common Regex Patterns
| Pattern | Matches | Example |
| :--- | :--- | :--- |
| `^[0-9]{1,3}\.` | Start of an IP | `192.` |
| `[a-zA-Z0-9._%+-]+@[a-z.-]+\.[a-z]{2,}` | Email Address | `user@mail.com` |
| `\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b` | Full IPv4 | `10.0.0.1` |
| `.*` | Everything | "Catch all" |

---

## 💻 CLI Navigation & Operations
| Shortcut | Action |
| :--- | :--- |
| `Ctrl + A` | Move cursor to start of line. |
| `Ctrl + E` | Move cursor to end of line. |
| `Ctrl + R` | **Reverse Search** (Search command history). |
| `!!` | Repeat the last command. |
| `sudo !!` | Repeat last command with sudo. |
| `!$` | Use the last argument of the previous command. |

---

## 🔍 Practical Examples (The "Power User" Mix)

### 1. Finding Active Users (CLI + Regex)
`grep -E '/bin/(bash|sh)$' /etc/passwd`
*Finds all users whose login shell is bash or sh.*

### 2. Extracting IP Addresses from a Log
`grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' access.log | sort -u`
*Extracts (`-o`) unique (`sort -u`) IPs using Extended Regex.*

### 3. Mass File Rename (Rename .txt to .bak)
`for f in *.txt; do mv "$f" "${f%.txt}.bak"; done`
*Uses shell expansion to change file extensions.*

---

## 💡 Expert Tip: Non-Greedy Matching
In some regex engines (like Python or Perl `-P`), `*` is "greedy" and grabs as much as possible. Use `.*?` to make it **Non-Greedy**, which stops at the first possible match. 
*Example:* `<div>.*?</div>` matches each div separately, whereas `<div>.*</div>` might match everything from the first `<div>` to the very last `</div>` on the page.
