# 🖥️ Windows System Administration & Diagnostics

> **Objective:** Maintaining system health, troubleshooting hardware/software conflicts, and ensuring data integrity through native Windows utilities.

---

## 🛠️ Essential Diagnostic Utilities

| Utility | Command | Primary Use Case |
| :--- | :--- | :--- |
| **System Configuration** | `msconfig` | Managing **Clean Boots**, disabling startup services, and troubleshooting boot-time driver issues. |
| **System Information** | `msinfo32` | Exhaustive summary of **Hardware Resources**, Components, and Software Environments (BIOS version, Secure Boot state, Driver versions). |
| **Event Viewer** | `eventvwr.msc` | The primary log for system, security, and application errors. |
| **Resource Monitor** | `resmon` | Real-time analysis of CPU, Disk, Network, and Memory "per-process" usage. |

---

## 🏗️ The Windows Registry
The Registry is a hierarchical database that stores low-level settings for the OS and applications. It is organized into **Hives**.



### Core Registry Hives
*   **HKEY_LOCAL_MACHINE (HKLM)**: System-wide settings (Hardware, Security, Software). These apply to all users.
*   **HKEY_CURRENT_USER (HKCU)**: Specific settings for the currently logged-in user (Desktop wallpaper, keyboard layout).
*   **HKEY_CLASSES_ROOT (HKCR)**: Information on file associations and OLE (Object Linking and Embedding).

> **Expert Note**: The Registry is often targeted by malware for **Persistence** (e.g., the `Run` or `RunOnce` keys), allowing code to execute automatically upon user login.

---

## 🛡️ Advanced Data Protection
Securing the Windows environment involves a combination of hardware-backed encryption and logical backup services.

### 1. BitLocker & TPM
*   **BitLocker**: Full Disk Encryption (FDE) that protects data at rest.
*   **TPM (Trusted Platform Module)**: A specialized hardware chip that stores cryptographic keys. It ensures the system hasn't been tampered with (Measured Boot) before releasing the BitLocker decryption key.

### 2. VSS (Volume Shadow Copy Service)
*   **Mechanism**: A framework that allows taking manual or automatic backup copies/snapshots of computer files or volumes, even when they are in use.
*   **Admin Use**: Used by "System Restore" and "Previous Versions" to recover accidentally deleted files.
*   **Red Team Context**: Attackers often attempt to delete Shadow Copies (`vssadmin delete shadows /all`) to prevent a victim from recovering their data after a ransomware attack.

---

## 💡 Expert Tip: The Diagnostic Workflow
When a system is experiencing "Blue Screen of Death" (BSOD) or instability:
1.  Use `msinfo32` to check for **Driver Conflicts** or outdated BIOS versions.
2.  Use `msconfig` to perform a **Diagnostic Startup** (loading only basic devices and services).
3.  If the issue persists in a clean boot, it is likely a **Hardware failure** (check RAM/Disk health).
