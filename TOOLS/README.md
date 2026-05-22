# 🧰 Security Toolkit & Cheat Sheets

> **Objective:** Atomic documentation for specific security tools. Each file contains syntax, flags, expert one-liners, and pentest workflows.

---

## ⚔️ Offensive Tools (Red Team)

### Reconnaissance & Enumeration
* **[Nmap](./nmap.md)**: Network discovery, port scanning, service enumeration, NSE scripts.
* **[Gobuster](./gobuster.md)**: Directory/file brute-forcing, subdomain & vhost discovery.
* **[DIRB](./dirb.md)**: Web content discovery (recursive directory scanning).

### Exploitation
* **[Metasploit](./metasploit.md)**: Exploitation framework, msfvenom payloads, Meterpreter.
* **[Burp Suite](./burpsuite.md)**: Web app intercepting proxy, fuzzing, and testing.
* **[Netcat](./netcat.md)**: Reverse shells, file transfer, port scanning, pivoting.

### Credential Attacks
* **[Hydra](./hydra.md)**: Network login brute-forcer (SSH, FTP, HTTP, SMB, RDP).
* **[Hashcat](./Hashcat.md)**: GPU-accelerated password cracking.
* **[John the Ripper](./john_the_ripper.md)**: CPU hash cracking, encrypted file cracking.

### Privilege Escalation
* **[LinPEAS](./LinPEAS.md)**: Automated Linux privilege escalation enumeration.

---

## 🛡️ Defensive Tools (Blue Team)

* **[Volatility](./volatility.md)**: Memory forensics and malware analysis.
* **[Snort](./snort.md)**: Network Intrusion Detection System (NIDS), custom rules.
* **[TCPdump](./tcpdump.md)**: Command-line packet capture and analysis.

---

## 💡 How to Use These Sheets

1. Find the tool by name
2. Review the **Essential Commands** section
3. Use the **Expert One-Liners** for quick copy-paste in engagements
4. Check **Common Mistakes** when something isn't working
5. Follow the **Links** section to chain tools together

---

## 📂 Tools Still to Document

- [ ] ffuf (web fuzzer)
- [ ] feroxbuster (recursive directory discovery)
- [ ] BloodHound (AD enumeration)
- [ ] CrackMapExec (AD swiss army knife)
- [ ] Impacket (Python network tools)
- [ ] Chisel (TCP/UDP tunneling)
- [ ] Ligolo-ng (pivoting)
- [ ] Responder (LLMNR/NBT-NS poisoning)
- [ ] Mimikatz (Windows credential dumping)
- [ ] Certipy (ADCS attacks)
- [ ] Evil-WinRM (WinRM shell)
- [ ] Rubeus (Kerberos attacks)
- [ ] Sliver (C2 framework)
- [ ] Wireshark (GUI packet analysis)
- [ ] Bettercap (MITM framework)
- [ ] Amass (subdomain enumeration)
- [ ] Nuclei (vulnerability scanner)
- [ ] sqlmap (SQL injection automation)
