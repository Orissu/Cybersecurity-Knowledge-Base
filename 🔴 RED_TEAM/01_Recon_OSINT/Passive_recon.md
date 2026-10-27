# Passive Reconnaissance

---

## Purpose

---

Passive reconnaissance consists to gather information without contacting the target using only public sources.
It is one of the most powerful and lowest-risk phase in pentesting, bug bounty and threat hunting.
Even with strong policies and high security, we can still get useful data publicky exposed through DNS, WHOIS, certficates logs, search engines and device census platforms.

The purpose of the attacker is to gather intelligence about the target to find weaknesses and to determine the window of attack.
The purpose of the defender is to understand what is there to know about them from public sources to help protect their system.

---

## WHOIS

---

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

  ---

  
