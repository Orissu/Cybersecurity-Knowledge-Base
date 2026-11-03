# Server Message Block Attacks

The Server Message Block (SMB) protocol is a client-server communication protocol used for sharing access to files, printers, serial ports etc.. on a network.
Servers make resources available on the network and client computers want access to those shared filesystems and printers on the servers.
SMB is a response-request protocol. It connects via NetBios over TCP/IP.
After the connection is established with the SMB server we can use smb commands to allow the server to access data.

Enumeration is the process of gathering information on a target in order to find potential attack vectors and aid in exploitation.
Enum4Linux is a tool to enumerate SMB shares on Windows and Linux systems. We use it like this `enum4linux [options] ip`

| Options | Description |
| --- | --- |
| `-U` | Get the user list |
| `-M` | Get the machine list |
| `-N` | Get the namelist dump |
| `-S` | Get the sharelist |
| `-P` | Get the password policy information |
| `-G` | Get the group and member list |

The best way to get into a system is to look for misconfigurations in the system.

SMBClient is a client to access ressources on smb servers. We can use ls or dir, or we can download a file with get.
