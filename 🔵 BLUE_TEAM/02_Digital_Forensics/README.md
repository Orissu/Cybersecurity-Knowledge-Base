# 🔍 Digital Forensics

> **Objective:** The scientific acquisition, preservation, and analysis of digital evidence to determine the timeline and impact of a security incident.

---

## ⚖️ The Order of Volatility
When investigating a live system, analysts must collect data based on how quickly it disappears (from most volatile to least volatile):
1.  **CPU Cache & Registers** (Nanoseconds)
2.  **Routing Table, ARP Cache, Process Table, Kernel Statistics** (Seconds)
3.  **Memory (RAM)** (Minutes/Hours)
4.  **Temporary File Systems / Swap Space**
5.  **Disk / Storage Media** (Years)
6.  **Remote Logs & Archived Backups**

---

## 🧠 Memory Forensics (RAM Analysis)
RAM contains "live" evidence not found on the disk, such as running processes, decrypted passwords, and active network connections.



### Acquisition
* **Tool:** `DumpIt.exe`
* **Process:** A lightweight CLI utility used to create a raw image of the Windows physical memory. 
* **Command:** Run `dumpit.exe` from an external drive to minimize the footprint on the target system.

### Analysis
* **Tool:** **Volatility Framework**. An open-source tool that uses plugins to parse memory dumps and reconstruct the state of the machine at the time of capture.

---

## 📄 Artifact & Metadata Extraction
Files often hide "data about data" (metadata) that can identify the creator or the software used.

### PDF Investigation
* **Tool:** `pdfinfo`
* **Command:** `pdfinfo <filename>.pdf`
* **Data Revealed:** Author, Creation Date, Modification Date, and the software used to produce the PDF (useful for identifying exploit-kit signatures).

---

## 📡 Network Forensics: Wireshark
While highly powerful, analysts must understand the operational limits of **Wireshark**.

* **Nature:** It is a **Passive Analyzer**. 
* **Limitations:** * No automated alerting system.
    * Cannot block traffic (IPS functionality).
    * Not suitable for long-term log retention.
* **Best Use Case:** Deep-packet inspection (DPI) once a suspicious stream has been identified by a SIEM or IDS.

---

## 🔗 Associated Tools
* [Volatility](../../TOOLS/volatility.md) - Advanced memory forensics.
* [Wireshark](../../TOOLS/wireshark.md) - Network protocol analysis.
* [Autopsy] - GUI-based disk forensics.
