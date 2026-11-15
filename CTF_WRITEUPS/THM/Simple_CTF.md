# Simple CTF – TryHackMe Write-up

| | |
|---|---|
| **Difficulty** | Easy |
| **Category** | Web / Linux PrivEsc / Active Recon |
| **Target OS** | Linux |
| **Date** | 14/11/2026 |

## Summary

## 1. Reconnaissance / Enumeration

   ### 1.1 Port scan
   
We first launch a nmap scan: `sudo nmap -sV -Pn -p- @IP`
We find three open ports in total and two open ports under the port 1000.
-port 21 FTP vsftpd 3.0.3
-port 80 http Apache httpd 2.4.18
-port 2222 SSH OpenSSH 7.2p2

   ### 1.2 Web enumeration

we can now use gobuster the do some web enumeration:
`gobuster dir -u http://@IP:80 -w /usr/share/wordlists/dirbuster/directory_2.3-medium.txt -x .php,.txt,.html`
The command gives us a lot of information such as a robot.txt to check and a `/simple` directory to check.

We can use gobuster to do web enumeration on this directory:
`gobuster dir -u http://@IP:80/simple -w /usr/share/wordlists/dirbuster/directory_2.3-medium.txt -x .php,.txt,.html`
The results are: `/modules`, `/index.php`, `/uploads`, `/doc`, `/admin`, `/assets`, `/lib`, `config.php`, `/tmp` and `/install.php`.

   ### 1.3 Other enumeration

After searching on the website directly, we notice that the website is running on CMS make it Simple 2.2.8.

## 2. Initial Access (Foothold)

   ### 2.1 Identified vulnerability

After searching for any vulnerability about this software, we found that there is a high CVE: CVE 2019-9053. We can conclude that the software is vulnerable to Time-based SQLi.
   
   ### 2.2 Exploitation

As it is tidious to try doing the SQLi by hand, we can look if there is an already existing exploit. For that, we use searchsploit: `searchsploit CMS make it Simple`.
We then find the exploit for SQLi for versions under 2.2.10, exploit 46635.py.
We can then download it with `searchsploit -m /php/webapps/46635.py` and look for the parameters needed. To use this exploit, we need to trnsform it from python2 to python3 using 2to3 and modify the code to be able to read the data in rockyou.txt.
We can now execute the code with : `python3 /root/46635.py -u http://@IP:80/simple -w /usr/share/wordlists/rockyou.txt --crack`
This command gives us the salt of the password hash, the username, the email, the hashed password and the cracked password.
   
   ### 2.3 Getting a shell

To have a sheel, we now can log into the target system with the SSH open port: `ssh user@IP -p 2222`.
We can use the credentials we found.
   
## 3. Privilege Escalation

   ### 3.1 Local enumeration

Now that we are connected we can look for the first flag. We can find it in the home repository using `ls -la`.
We can explore the `/home` of other users using `ls -la /home`.
By using `sudo -l`, we find that the vim service can be used by our session with sudo without any password.
   
   ### 3.2 Exploiting the vector

The goal is to use vim to get a root shell. To do this we can use this command: `sudo vim -c ':!/bin/bash'`.
   
   ### 3.3 Root access

This command gives us a root shell with root privileges. With that we can find the flag in /root/root.txt.

## 4. Dead Ends and Difficulties (optional)

Finding the CVE and setting up the exploit.

## 5. Remediation

Research on how to do it.

## 6. Lessons Learned

Searchsploit.
