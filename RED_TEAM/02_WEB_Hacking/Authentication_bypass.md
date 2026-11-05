# Authentication Bypass

To find Authentication vulnerabilities, we can use the error messages that websites return to get information about how the system works.
When filling a form of a new user, we can list which login already exits. It gives us information about the users.

The fuff tool allows to check for already existing logins by using a wordlist of commonly used usernames.

| Option | Purpose |
| --- | --- |
| `-w` | to specify to file location |
| `-X` | to specify the request method to use (GET by default) |
| `-d` | to specify the type of data |
| `-H` | to add addtionnal headers to the request |
| `-u` | to specify the target url |
| `-mr` | text we're looking for to validate we've found a valid username |
| `-fc` | to check for an HTTP status code different from 200 |

The FUZZ keyword is used to specify where the content from the list will be inserted.
If there are two FUZZ keywords, we can replace them by W1 and W2.

---

## Logic Flaw

A logic flaw is when the typical logical path of an application is either bypassed, circumvented or manipulated by an attacker.
intented path : 1-->2-->3-->4
Attacker path : 1-->4

---

## Cookie Tampering

Examining and editing the cookies set by the web server can give information such as unauthentication access, access to another user's account or elevated privileges.
Some cookies have their content readable and in plain text, thus giving us an easy way to read and manipulate them.
