# 🛠️ Burp Suite

> **Usage:** Intercepting Proxy and Web Application Security Testing.

## 📋 Module Overview

| Module | Purpose | Expert Use Case |
| :--- | :--- | :--- |
| **Proxy** | Intercept/Modify traffic. | Modifying price parameters in a checkout cart. |
| **Repeater** | Manual re-sending. | Testing for LFI by manually changing file paths. |
| **Intruder** | Automated spraying. | Fuzzing for hidden parameters or brute-forcing logins. |
| **Decoder** | Transformation. | Converting Base64 strings or URL-encoding payloads. |
| **Comparer** | Byte-level diff. | Comparing two different responses to find subtle SQLi clues. |

## 💡 Expert Tip
Always check the **HTTP History** tab in the Proxy module. Even if you aren't intercepting, Burp logs every request, which is vital for finding forgotten API endpoints or headers.
