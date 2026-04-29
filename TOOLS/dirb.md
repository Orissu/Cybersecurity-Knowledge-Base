# 🛠️ DIRB (Web Content Scanner)

> **Category:** Content Discovery / Web Hacking

## 📝 Description
DIRB is a specialized tool that launches a dictionary-based attack against a web server and analyzes the HTTP responses. It is used to find hidden directories, unlinked pages, and sensitive files.

## 📋 Cheat Sheet

| Command | Description |
| :--- | :--- |
| `dirb <url>` | Simple scan using the default wordlist. |
| `dirb <url> <wordlist>` | Scan using a specific dictionary (e.g., `/usr/share/wordlists/dirb/big.txt`). |
| `dirb <url> -X .php,.txt` | Filter results to only show files with specific extensions. |
| `dirb <url> -i` | **Case Insensitive**: Useful when scanning Windows-based web servers. |
| `dirb <url> -a <user-agent>` | Custom User-Agent to bypass simple WAF rules. |

## 💡 Expert Tips

### Recursive vs Non-Recursive
By default, DIRB is **recursive** (it will scan found sub-directories). To save time on large targets, use the `-r` flag to disable recursion.

### Optimizing with Status Codes
If the server returns too many false positives (e.g., custom 404 pages), use the `-N` flag to ignore specific HTTP response codes:
`dirb http://target.com -N 302`

---

**See Also:** [Gobuster](gobuster.md), [FFUF](ffuf.md) (Modern alternatives for faster multi-threaded discovery).
