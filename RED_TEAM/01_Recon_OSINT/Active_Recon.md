# Active Reconnaissance


Active Reconnaissance is the of process of directly interacting with a target system or network to gather information about it.
It requires tranmitting packets, making connections and probing services.

Active Reconnaissance leaves traces like IDS Alerts, WAF blocks or honey pot triggers. 
In 2026, Active Reconnaissance is at a point where it is the most riskier and detectable ever in history. Most of companies depoy lots of services in addition of traditionnal WAFs to add a layer of security.
For a Red Team perspective, the role is to **blend in**.

---

## Web Browser

The Web browser is one of the most convenient and least suspicious tools for Active Reconnaissance as there is traffic present on virtually every system. Moreover, We can blend in with a normal user.

HTTP/3 uses the QUIC protocol (transport Layer). It combines the function of TCP and TLS into a single protocol running on UDP port 43.
