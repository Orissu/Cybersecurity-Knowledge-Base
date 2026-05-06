# ⌨️ CLI Proficiency & Pattern Matching

> **Objective:** Mastering the command-line interface and Regular Expressions (Regex) to automate data parsing and system administration.

---

## 🏗️ CLI Syntax Fundamentals
The Shell (Bash/Zsh) interprets characters before passing them to a command. Understanding "Shell Expansion" is critical for avoiding errors.

### 1. Quotation Mechanics
*   **Single Quotes (`' '`)**: Literal interpretation. Every character inside is treated as text (no variable expansion).
*   **Double Quotes (`" "`)**: Semi-literal. Allows variable expansion (`$VAR`) and command substitution.
*   **Backticks (`` ` ``) / `$( )`**: Command substitution. Executes a command and inserts the output.

### 2. Stream Redirection & Piping
*   `>` : Redirect output (Overwrite).
*   `>>` : Redirect output (Append).
*   `2>` : Redirect errors (stderr).
*   `|` : Pipe. Takes the `stdout` of one command and sends it as `stdin` to the next.

---

## 🧩 Regular Expressions (Regex)
Regex is a language for describing patterns in text. It is used by `grep`, `sed`, `awk`, and most programming languages.



### 1. The Anchors
*   `^` : Matches the **Start** of a line.
*   `$` : Matches the **End** of a line.

### 2. Quantifiers (How Many?)
*   `*` : 0 or more.
*   `+` : 1 or more.
*   `?` : 0 or 1 (Optional).
*   `{n,m}` : Between `n` and `m` times.

### 3. Character Classes
*   `.` : Any single character except a newline.
*   `[a-z]` : Any lowercase letter.
*   `[^0-9]` : Any character **NOT** a digit.
*   `\d` : Any digit (equivalent to `[0-9]`).
*   `\w` : Any word character (Alphanumeric + underscore).

---

## 💡 Expert Tip: Grep Flavors
By default, `grep` uses Basic Regular Expressions (BRE). For advanced patterns (like `+`, `?`, or `|`), use **Extended Regex (ERE)** with the `-E` flag:
`grep -E '^(admin|root)' /etc/passwd`
