# Pickle Rick – TryHackMe Write-up

| | |
|---|---|
| **Difficulty** | Easy |
| **Category** | Web / PrivEsc |
| **Target system** | Linux |
| **Date** |03/11/2026 |


## 1. Reconnaissance / Enumeration

   ### 1.1 Port scan

command : `nmap -Pn -sV -p- @IP`
We find that it is a Linux OS and that there are two open ports : 

- port 22 for SSH, OpenSSH 8.2p1 Ubuntu
- port 80 for http, Apache httpd 2.4.41

There's a web server, so we will try to enumerate the directories and important files using Gobuster.

   ### 1.2 Web enumeration

We use gobuster and we use this command : `gobuster dir -u http://@IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.txt,.html`
the `-x` option lets gobuster enumerate certain types of files, for this case we want to see if there's php files, txt files or hidden html files.

After this we find lots of files and directories but 3 of them are interesting : login.php, robots.txt and clue.txt
We can now go on the website and search on the website for vulnerabilities or comments or anything left by the developers.
Using the developer's tools, we find the login in index.html: R1ckRul3s
After looking into the website, there isn't anything in the .js files. Now we can explore those interesting files we found when using gobuster. First clue.txt : it contains a clue to where to find the 2nd flag.
Robots.txt contains the word : Wubbalubbadubdub
   
## 2. Initial Access (Foothold)

   ### 2.1 Identified vulnerability
The vulnerability is to have credentials in clear text in files accessible by everyone, security vulnerability. Once we have the credentials we can have access to the data.


   ### 2.2 Exploitation

The only panel we have access to is the command panel. We try to test some commands. First, we use `whoami` to know who we are. the console returns www-data.
Then, we can list everyting in the directory using `ls -la` and we find a weird .txt file. To see the file, we can't use `cat` or `head` or `tail` as the commands are blocked so we use `less`. We find the first flag.

## 3. Privilege Escalation

   ### 3.1 Local enumeration

Now we want to see if our process has admin privileges. to do so we use `sudo -l` to list our privileges. We see that our process can run every command without a password.

   ### 3.2 Exploiting the vector

As we can execute every command without a password, we can go see the local user folers to see if there are important documents. We use `sudo ls -la /home`. We see that there's a folder Rick, and by seeing what's in there, we can see the second flag.

   ### 3.3 Root access
   
We can go see the /root folder to see if there are interesting data. To do that we use `sudo ls -la /root`. We see a .txt file 3rd.txt, by opening it, we get the 3rd flag.

## 5. Dead Ends and Difficulties (optional)

I had difficulties finding the second flag because when I listed the users, Rick didn't appear in it. But it seems that it isn't because there aren't users that there isn't a home directory.

## 6. Remediation

Looked in /home.

## 7. Lessons Learned

Osint and -x option for gobuster to enumerate files.
the command `less` to replace cat.
The user issue.
`sudo -l` to list the rights and have admin privileges.

