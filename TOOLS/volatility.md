# 🛠️ Volatility Framework

> **Usage:** Advanced Memory Forensics and Incident Response.

## 📋 Common Plugin Commands
*Note: Syntax varies between Volatility 2 and Volatility 3 (the modern standard).*

| Plugin | Description | Expert Insight |
| :--- | :--- | :--- |
| `windows.pslist` | Lists running processes. | Look for suspicious parent-child relationships (e.g., `cmd.exe` spawned by `notepad.exe`). |
| `windows.netscan` | Shows active network connections. | Identifies C2 (Command & Control) callbacks in real-time. |
| `windows.malfind` | Locates injected code/DLLs. | Essential for finding "Fileless Malware" residing only in memory. |
| `windows.filescan` | Lists open files in memory. | Useful for finding accessed documents or dropped malicious scripts. |

## 💡 Expert Tip
When starting an investigation with Volatility 3, always begin with `windows.info` to confirm the OS architecture and symbols. This ensures that subsequent plugins interpret the data structures correctly.
