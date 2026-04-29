# 🛠️ TCPdump

> **Usage:** Command-line packet analyzer. Essential for remote troubleshooting and forensic captures.

## 📋 Essential Capture Commands

| Command | Description |
| :--- | :--- |
| `tcpdump -i any` | Listen on all active interfaces. |
| `tcpdump -i <eth0> -w capture.pcap` | **Write** output to a PCAP file for Wireshark analysis. |
| `tcpdump -c 100` | Stop automatically after capturing **100** packets. |
| `tcpdump -r file.pcap` | **Read** and display a previously saved PCAP file. |

## 🔍 Display & Formatting (Resolution Flags)
Use these to make output human-readable or raw:
* `-n`: Disable DNS resolution (shows IPs). Faster and safer.
* `-nn`: Disable port name mapping (shows `80` instead of `http`).
* `-v / -vv / -vvv`: Increase verbosity (shows TTL, ID, length, etc.).
* `-A`: Print payload in **ASCII** (best for unencrypted HTTP, FTP, or SMTP).
* `-X`: Show both **Hex** and **ASCII** (best for binary protocols).
* `-e`: Display **Layer 2** information (MAC addresses).

## 🛠️ Advanced Filtering (BPF - Berkeley Packet Filter)

### Basic Logic
Combine filters using `and` (`&&`), `or` (`||`), or `not` (`!`).
* `host 192.168.1.10`: Traffic involving a specific IP.
* `net 192.168.1.0/24`: Traffic for a whole subnet.
* `port 443`: Target specific services.

### Expert Bitmasking & Offsets
Isolate specific TCP control bits:
* `tcp[tcpflags] == tcp-syn`: Only SYN packets (connection attempts).
* `tcp[tcpflags] & (tcp-syn|tcp-ack) != 0`: SYN or SYN-ACK packets.
* `greater 1000`: Capture only large packets (often used to find data exfiltration).

## 💡 Expert Tip
When analyzing unencrypted traffic, use `-A -vv` to see the actual content of the packets (passwords, cookies, commands) directly in your terminal.
