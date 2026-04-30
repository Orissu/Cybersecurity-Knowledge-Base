# 🔐 Cryptography & InfoSec Fundamentals

> **Definition:** The science of transforming information into an unreadable format to protect data at rest, in transit, and in use.

## 🏛️ The CIA + N Triad
| Pillar | Description | Mechanism |
| :--- | :--- | :--- |
| **Confidentiality** | Only authorized users can read data. | Encryption (AES, RSA). |
| **Integrity** | Data hasn't been altered. | Hashing (SHA-256). |
| **Availability** | Systems are accessible. | Redundancy & Backups. |
| **Non-Repudiation** | Actions cannot be denied. | Digital Signatures. |

---

## ⚙️ Encryption Deep Dive

### 1. Symmetric Encryption (Shared Secret)
Both parties use the **same key**. 
*   **Stream Ciphers**: Encrypt data bit-by-bit (e.g., ChaCha20).
*   **Block Ciphers**: Encrypt data in fixed-size chunks (e.g., AES-256).
*   **Best For**: Bulk data encryption (Disk, Database).

### 2. Asymmetric Encryption (Public-Private Key)
Uses a mathematically linked **Key Pair**.
*   **RSA**: Based on the difficulty of factoring large prime numbers.
*   **ECC (Elliptic Curve)**: Provides the same security as RSA but with much smaller keys (faster).
*   **Best For**: Key exchange, Digital Signatures, SSH.

### 3. Hashing (One-Way Functions)
A deterministic algorithm that turns any input into a fixed-length string.
*   **Collision Resistance**: Two different inputs should never produce the same hash.
*   **Avalanche Effect**: Changing 1 bit of input changes >50% of the output.
*   **Common Algos**: SHA-256, SHA-3, Argon2 (for passwords).

---

## 🏗️ Digital Chain of Trust (PKI)
1.  **Root CA**: Highly secure entity (e.g., DigiCert) that signs its own certificate.
2.  **Intermediate CA**: Signed by the Root to issue certificates to end-users (improves OpSec).
3.  **End-Entity**: The certificate on your website.
4.  **CRL/OCSP**: Mechanisms to check if a certificate has been **revoked** before its expiry.
