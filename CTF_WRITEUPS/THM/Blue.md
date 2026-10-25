# Blue

> **Purpose :** Capture the Flag 
> **Machine Level :** Easy

 ---
 
## First Step : Recon

- The first step is to scan the machine using nmap.
- We can use the command `nmap -Pn -sS -A --open --script vuln <IP>` to scan it and finding potential vulnerabilities.
- The machine has three open ports below 1000 and we find that the machine is vulnerable to ms17-010.

---

## Second Step : Gain access

- We can then launch metasploit using `msfconsole` to find if there is an exploit to this vulnerability.
- We find the exploit `exploit/windows/smb/ms17_010_eternalblue`.
- We configure it by setting RHOSTS and the room tells us to set the payload `windows/x64/shell/reverse_tcp`.
- We can now launch the exploit and create a session.

---

  ## Third Step : Escalate
  
- now that we have a session to the machine, we need to convert the shell into a meterpreter shell in order to escalate our privileges.
- To do that, we can use the post module `post/multi/manage/shell_to_meterpreter`
- We only hqve to chqnge the option `SESSION` to match our backgrounded session.
- Now, we can launch our post module to escalate our shell.
- We now have to make sure that our session has escalated to NT AUTHORITY/SYSTEM. To do that we can use the command `getsystem`
- We can now list all of our processes to find one that has admin privilege and migrate to it with meterpreter using `migrate`

  ---

  ## Fourth Step : Craking
  
- By using the command hashdump, we can find every hashes on the computer
- We can then use hashcat and the command `hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt` to decrypt the hashes and have access to the Administrator's credentials.

  ---
  
  ## Fifth Step : Find flags!
  
- The flags are at strategic places in the machine. The first one is at the roo C:\
- The second one is where windows keeps its passwords : in the SAM file
- The last one is in the personnal documents of the Administrator.

  ---
