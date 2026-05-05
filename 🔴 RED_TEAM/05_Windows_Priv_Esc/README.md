# 🪟 Windows Privilege Escalation & Metasploit

> **Objective:** Leveraging the Metasploit Framework (MSF) to transition from initial access to Administrative or SYSTEM-level privileges on Windows targets.

---

## 🏗️ The Exploitation Trinity
Understanding the relationship between these three components is fundamental to any successful attack:

1.  **Vulnerability**: The "Hole" or weakness in the system (e.g., an unquoted service path or a buffer overflow).
2.  **Exploit**: The "Key" or the code that takes advantage of the vulnerability to gain entry.
3.  **Payload**: The "Action" or the code that runs after the exploit is successful (e.g., a reverse shell).



---

## 🛡️ Bypassing Defenses

### Signature-Based Antivirus (AV)
Traditional AV matches file patterns against a database of known threat signatures.
*   **Encoders**: Reformat shellcode to change its signature (e.g., `shikata_ga_nai`). Note: Modern AV is often effective against simple encoding.
*   **Evasion Modules**: Specialized MSF modules that use advanced techniques to obfuscate the exploit's behavior.
*   **Adapters**: Wrappers that convert payloads into environment-specific formats like PowerShell to blend in with legitimate system activity.

---

## 🧪 Memory Exploitation (Buffer Overflow)
When exploiting service vulnerabilities at the memory level, precision is required:
*   `pattern_create`: Generates a unique string to find exactly where the memory "overflows."
*   `pattern_offset`: Identifies the exact distance (offset) to the EIP (Instruction Pointer) to control the flow of execution.

---

## 🔗 Associated Tools
* [Metasploit & msfvenom](../TOOLS/metasploit.md) - The primary framework for Windows exploitation.
* [PowerUp] - PowerShell script for local Windows privilege escalation enumeration.
