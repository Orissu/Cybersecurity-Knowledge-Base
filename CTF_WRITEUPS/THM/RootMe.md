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
'''bash
'''
- This shell lets us enter commands.
- To have access to this shell, we use netcat.
  On the attacker machine :
  ```bash
  nc -lvnp 4444
  ```

  On the victim machine we can use two different techniques.
  First one is to enter the command i the url with this :
  <--TODO-->

  Second is to <--TODO-->

---

  ## Third Step

- After we hace access to the shell in a terminal, we can find the files with SUID permissions
- the purpose of this is to do a privilege escalation to gain access to root
- to find the files we use ```bash find / -perm 4000 -type f 2>/dev/null ```
- We find a python software with SUID permissions, we can use that to escalate our permissions.
- we can use this script to get to the root :
- ```bash
  <--TODO-->
  ```
  -Finally we can go get the information to the root directory and find the flag in root.txt

  ---
