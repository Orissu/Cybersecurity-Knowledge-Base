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

Tips : 
- The User-Agent Switcher and Manager extension can be detected by WAFs and CDNs if there are too many changes.
- The wappalizer extension can be spotted by Modern WAFs nowadays.

--- 

## Ping

Ping uses the Internet Control Message Protocol (ICMP) to function.
It sends an ICMP echo request and if the socket reaches the target and if the latter is allowed to answer it sends back an ICMP echo Reply. The exchange is fast and light.
to use Ping : `ping -c NUMBER @IP/DOMAIN_NAME` on linux. (-n for Windows)
We can use the options -4 or -6 to force the IP version that we want to use.

- if the ping is successful : the target is online and reachable.
- If we don't have a reply there's two possibilities.
  If we receive "Destination Host unreachable" then the host is down.
  If there's 100% packet loss and no error, it means that ICMP is filtered or blocked on the target's side.

---

## Traceroute

This command traces the route that packets take from our machine to the target host.
The goal is to discover the IP addresses of the routers in the path to count them.
It is useful for network topology, identifying where filtering or latency occurs and mapping infrastructures.

As there are many routing protocols that optimize the network (OSPF, BGP, RIP...), the route taken by the packets is not fixed and may not be the same everytime.

to use traceroute : `traceroute [-6] @IP` (tracert on Windows)
Traceroute exploits the Tme To Live (TTL) field in the TCP headers. Each time a packet passes through a router, the TTL decreases from one. 
When the TT field reaches 0, it won't keep being transferred by the router and it will die. The router then sends a Time To Live exceeded message to inform the user that the route is too long for the packets to be transferred.
Nowadays some routers are configured to not send those TTL exceeded messages.

On Linux, traceroute sends by default UDP datagrams. TO change this we use the options `-T` for TCP and `-I` for ICMP.

Tips : use mtr (my traceroute) for real-time continuous views with trace, ping-like stats, packet loss and latency per hop.

---

## TELNET (Teletype Network)

It is a protocol used to communicate to a remote system via a CLI.
It can be used for Remote administration on port 23. The issue is that this protocol sends all the data in a clear Text, therefore it is really easy to get the information.
The secure alternative to telnet would be The Secure SHell protocol (SSH).

Then why do we use this protocol?
It operates over TCP so we can use it to connect to any TCP port and observe the server's response.
It is called **banner banning** : the act to connect to a service and read the initial response (the banner).
Banners reveal the software's name and version running at that port.

Nowadays, netcat and curl are preferred for banner grabbing as they more flexible.

to use telnet : `telnet @IP PORT`
Depending on the service, the way to trigger a response isn't the same. For HTTP it is : `GET / HTTP/1.1` and enter 2 times.

for HTTPs, we use curl : 
`curl --head https://@IP/DN`
We can also use openssl : `s_client_connect @IP:443`

---

## Netcat

[see the netcat sheet](../../TOOLS/netcat.md)
