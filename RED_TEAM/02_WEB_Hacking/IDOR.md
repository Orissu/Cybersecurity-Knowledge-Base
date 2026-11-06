# IDOR

Web Applications rely on identifiers to distinguish one object from another one. Each identifier contains or have a reference number or string.
This reference is used to locate them. 
When an application allows the user to supply that refernce and find the identifier without checking if the user is permitted to access it, it results in a **Insecure Direct Object Reference (IDOR)**.
It is an access control vulnerability and it sits within the Broken Access Control category in OWASP 10.

This vulnerability is significant because of the gap between the simplicity to exploit it and the impact it can make.
The sumplest form of IDOR occurs when there's an Object Identifier in a URL parameter and the attacker uses it to query a back-end database without an authentication check.

## Finding IDORs in Encoded IDs

Developers usually encode identifiers before including them in query strings. The most common encoding scheme is the base64.
How to exploite encoded IDs : 
- First we need to decode the value from the request
- We can modify the decoded output to reference a different object
- We can now encode the new value
- Final step is to substitue the re-encoded string back into the request and submit it.

## Finding IDORs in hqshed IDs

If the ID is hashed, we can find the decoded data if we have the hash algorithm and the type of the data. We cna reproduce the hashing process by bruteforcing every possibility.
- first step : find the algorithm
- second step: hash a range of likely input values
- Third step: Compare the results with the hashed ID

## Finding IDORs in unpredictable IDs

the ID can be randomly generated strings or UUIDs or else. This technique only eliminates enumeration.
To resolve that we can use the two-account technique:
- Create two accounts on the application
- log into the first account to record the identifiers associated with its ressources
- log into the second account and substitue the first account's identifiers into the second account's requests.

Vulnerable endpoints can exist anywhere the application processes a user-supplied object reference.

It can be seen in background requests such as API requests, in .js files that frequently contain references to API endpoints or even in parameter mining.
Parameter mining is a cybersecurity technique used to discover hidden, undocumented, or unlinked HTTP parameters within a web application.
