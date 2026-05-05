# 🌐 Secure Network Architecture

> **Objective:** Implementing cryptographic controls to protect the communication plane and ensure the reliability of networked systems.

---

## 🏛️ The Foundations of Trust (CIA + N)
Every network security control is designed to satisfy one or more of these pillars:

*   **Confidentiality**: Protection against unauthorized access (Privacy).
*   **Integrity**: Protection against unauthorized changes (Accuracy).
*   **Availability**: Protection against downtime (Uptime).
*   **Non-Repudiation**: The inability to deny an action (Accountability).

---

## 🔐 Cryptographic Implementation in Networking

### 1. Symmetric Encryption (Speed & Volume)
In network architecture, symmetric encryption is used for the **Data Plane**—encrypting the actual payload of a conversation.
*   **Shared Secret**: Both endpoints have the same key.
*   **Challenge**: How do you share the key over an insecure network? (Answer: Key Exchange protocols).
*   **Common Use**: Encrypting a VPN tunnel or the bulk data in an HTTPS session.

### 2. Asymmetric Encryption (Identity & Exchange)
Used for the **Control Plane**—establishing identity and securely exchanging keys.
*   **Public Key**: Can be freely distributed. Anyone can use it to **encrypt** a message to you.
*   **Private Key**: Must be kept secret. Only you can use it to **decrypt** those messages.
*   **Common Use**: SSH handshakes, SSL/TLS handshakes, and PGP email.

### 3. Hashing & Digital Signatures (Verification)
*   **Hashing**: A "One-Way Fingerprint." If the data changes by even one bit, the hash changes. It ensures **Integrity**.
*   **Digital Signatures**: Created by encrypting a hash with a **Private Key**. 
    *   *Verification:* Anyone with the Public Key can decrypt the hash and verify that only the owner of the Private Key could have sent it. This ensures **Non-Repudiation**.

---

## 🔑 Practical Application: SSH Key-Based Auth
Standard password-based authentication is vulnerable to brute-force and phishing. **SSH Keys** move the security from "Something you know" to "Something you have."

### The Handshake Workflow
1.  **Generation**: You create a key pair on your machine.
2.  **Registration**: You place your **Public Key** on the remote server (`~/.ssh/authorized_keys`).
3.  **Challenge**: When you connect, the server sends a challenge encrypted with your Public Key.
4.  **Response**: You use your **Private Key** to decrypt the challenge and prove your identity.

---

## 🛡️ Architecture Strategy: Defense in Depth
| Layer | Cryptographic Control | Goal |
| :--- | :--- | :--- |
| **Identity** | Digital Signatures / SSH Keys | Ensure the user is who they claim to be. |
| **Transit** | TLS / Symmetric Encryption | Protect data from sniffers (Man-in-the-Middle). |
| **Storage** | Hashing (for passwords) | Ensure data is useless if the database is leaked. |

---

## 💡 Expert Tip: Perfect Forward Secrecy (PFS)
In modern network architecture, always aim for **PFS**. This ensures that even if a server's long-term Private Key is compromised in the future, it cannot be used to decrypt *past* recorded sessions. This is achieved by generating unique session keys for every single connection.
