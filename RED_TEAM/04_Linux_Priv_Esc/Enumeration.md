# Enumeration

When we already got access of the linux machine, There are a lot of inofrmation we can look into to know the system better.

| What we look for | explanation |
| --- | --- |
| `hostname` | to know the hostname of the machine |
| `uname -a` | This command will print system information about the kernel |
| `/proc/version` | the proc filesystem gives information about the target system processes |
| `/etc/issue` | contains information about the OS (it's easily changd or customised |
| `ps aux` | gives a lot of information concerning processes on the target |
| `env` | This command will show environmental variables |
| `sudo -l` | list all commands that can be user by the user using sudo |
| `ls -la` | lists every subdirectory and file in the directory |
| `id` | gives a general overview of the user's privilege level and group membership (can be used on another user) |
| `/etc/passwd` | gives an easy way to discover users on the system |
| `history` | shows the history of commands |
| `ifconfig` | gives information about the network interfaces |
| `ip route` | shows the route table |
| netstat | shows existing communications (see cheatsheet) |
| `find` command | helps finding documents or information on the system |

## Automated enumeration tools

Those tools allow us to do enumeration a lot faster and easier. They should be used to save time knowing they may miss some privilege escalation vectors. Some examples of tools: LinPeas, LinEnum, Linux Exploit Suggester (LES).
