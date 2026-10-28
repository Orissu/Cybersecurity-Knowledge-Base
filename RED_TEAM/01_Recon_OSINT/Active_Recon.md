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

The Developer Tools are one of the most used tools. To see them we can use `ctrl+shift+I`.

| Name of the tab | Purpose |
| --- | --- |
| Network Tab | Displays all requests and responses in real time (headers, timing data, cookies, status codes...) |
| Console Tab | allows to execute JavaScript snippets directly in the page context, view errors and have interactions with the DOM |
| Source Tab | Allows to browse the existing web files (.js, .css, .html...) |
| Application Tab | located under the storage section, it can inspect cookies, the local storage and the session storage |
| Security Tab | It contains certificate details such as issuers, the validity period, SANs) |

Tips : 
- .js files frequently contain hardcoded API endpoints, directory structures, references to internal services and dev comments.
- Session storage sometimes contain session tokens, API Keys, Tracking IDs and authentication data.

There are lots of web extensions that can be used to get information : 

| Name of the extension | purpose |
| --- | --- |
| FoxyProxy | used to switch between proxys such as Burpsuite, ZAP, SOCKS5...) |
| User-Agent Switcher and Manager | Changes the user Agent string to emulate different browsers, OS or devices. |
| Wappalyzer | it automatically identifies the technologies used such as CMS platforms, web servers, Js frameworks... |

