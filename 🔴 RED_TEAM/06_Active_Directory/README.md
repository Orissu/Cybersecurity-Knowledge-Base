# 🏛️ Active Directory (AD) Exploitation

> **Objective:** Understanding the centralized management system of Windows networks to achieve Domain-wide compromise.

---

## 🧠 Core Infrastructure Components

### 1. The Domain Controller (DC)
The **Domain Controller** is the nerve center of the network. It hosts the **Active Directory Domain Services (AD DS)** and is responsible for:
*   **Authentication**: Verifying the identity of users and computers.
*   **Authorization**: Managing permissions via the **NTDS.dit** database (which contains all domain usernames and password hashes).
*   **Replication**: Synchronizing directory data with other DCs for redundancy.



### 2. Group Policy Objects (GPOs)
GPOs are the primary method for administrators to implement specific configurations across the network.
*   **Scope**: Applied to **Organizational Units (OUs)**, Sites, or Domains.
*   **Red Team Interest**: Misconfigured GPOs can be exploited to push malicious scripts, add "Domain Admin" users, or disable security software across thousands of machines simultaneously.

---

## 🔑 Authentication Protocols: The Primary Attack Surface

Active Directory relies on two main protocols. Understanding their differences is key to choosing your attack vector.

| Protocol | Type | Key Vulnerabilities |
| :--- | :--- | :--- |
| **Kerberos** | **Ticket-Based** | **Kerberoasting**: Requesting service tickets and cracking them offline. **AS-REP Roasting**: Exploiting users with "Do not require pre-auth" enabled. |
| **NetNTLM** | **Challenge-Response** | **SMB Relay**: Intercepting a hash and "relaying" it to another machine to gain access without cracking it. **Poisoning**: Using LLMNR/NBT-NS spoofing (Responder). |



---

## 🛡️ AD Logical Structure
*   **Forest**: The uppermost container; a collection of one or more Domains.
*   **Domain**: A logical group of network objects (users, computers, groups) that share a common AD database.
*   **Organizational Unit (OU)**: Sub-containers within a domain used to organize objects and apply GPOs.
*   **Trusts**: Relationships that allow users in one domain to access resources in another.

---

## 🛠️ Essential AD Tooling (Preview)
*   **BloodHound**: Uses graph theory to map attack paths and identify "Hidden" Domain Admins.
*   **Mimikatz**: The industry standard for extracting plain-text passwords and hashes from memory (LSASS).
*   **Responder**: A tool used to capture NetNTLM hashes via LLMNR/NBT-NS poisoning.
*   **Impacket**: A collection of Python classes for working with network protocols (includes `psexec`, `secretsdump`, and `wmiexec`).

---

## 💡 Expert Tip: The "Golden Ticket"
Once a Domain Controller is compromised, attackers often extract the **KRBTGT** account hash. This allows the creation of a **Golden Ticket**, providing the attacker with persistent, forged Kerberos Ticket Granting Tickets (TGTs) that grant administrative access to *any* resource in the domain for years.
