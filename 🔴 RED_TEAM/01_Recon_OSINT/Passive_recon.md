# Passive Reconnaissance


## Purpose

Passive reconnaissance consists to gather information without contacting the target using only public sources.
It is one of the most powerful and lowest-risk phase in pentesting, bug bounty and threat hunting.
Even with strong policies and high security, we can still get useful data publicky exposed through DNS, WHOIS, certficates logs, search engines and device census platforms.

The purpose of the attacker is to gather intelligence about the target to find weaknesses and to determine the window of attack.
The purpose of the defender is to understand what is there to know about them from public sources to help protect their system.

---

## WHOIS

WHOIS is a query/response protocol (RFC3912).
WHOIS servers listen on TCP port 43 qnd provide registration details for domain names.
The one maintaining these records is called the Domain registrar.
It gives a lot of information such as the name and the organisation but some iof it is blocked nowadays because of the GDPR.
As WHOIS is becoming obsolete, it is now replaced by the Registration Data Access Protocol (RDAP).
RDAP uses HTTPs, returns structured JSON, supports internationlaisation, provides better privacy controls...
 To use RDAP we can use the command : `curl -s https;//rdap.verisign.com/com/v1/domain/example.com | jq`
 The `| jq` option allows the output the JSON for readability. Verisign is usually used fro .com queries.

 When using RDAP, we tend to look for : 
 - redirection chains
 - Dates : useful for estimating the Company age
 - Name servers : potential new targets
 - Status : locked domains are harder to hijack.

  ---

  ## NSLookup and Dig

Both tools are used to query DNS records. It translates domain names into IP address, it finds mail servers...
Dig is the modern version of Name Server Lookup. It has a cleaner output, it sends TTL values and it is more reliable.

To use NSLookup we can use the command `nslookup -type=TYPE DOMAIN_NAME [SERVER]`
Where the parameter -type defines the record type and the parameter [SERVER] is optionnal and is used to define the resolver we want to use.

To use dig we can use : `dig [@SERVER] DOMAIN_NAME [TYPE]`

| RECORD TYPE | Result |
|---|---|
| A | IPv4 |
| AAA | IPv6 |
| CNAME | Canonical Name : an alias that points one Domain Name to another |
| MX | Mail Servers |
| SOA | Start Of Authority, primary name server, admin email, zone Serial Number |
| TXT | Text Records | 

tips : using public resolvers like 1.1.1.1 allows to avoid your ISP logging your queries.

---

## DNS Dumpster

Dig and NSlookup only reveal names that we know. It doesn't show the subdomains.
Subdomains are important because they often expose forgotten or vulnerable services.
DNS Dumpster aggregates public DNS Data from lots of sources and remains passive.
The results include subdomins and hosts, resolved IPs with geolocation, MX, TXT and CNAME records and visual maps showing the relationship between these.

The most effective passive subdomain discovery method is certificate transparency logs, accessible through crt.sh
Certificate transparency is a public logging framewrk that records every TLS/SSL certificates issued by a trusted authority.
It has a field named Subject Alternative Name (SAN) that lists everything.

