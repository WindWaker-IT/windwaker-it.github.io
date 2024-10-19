---
title: "[CTF] HackTheBox - Fawn (Basics)"
date: 2024-09-13 13:00:00 -500 
categories: [CTF - HackTheBox, Fawn (Basics)]
tags: [ctf,exploit,flags,htb,hackthebox]
author: Christoph K
---


Tasks:

- Connect
- Task 1:  What does the 3-letter acronym FTP stand for?
- Task 2:  Which port does the FTP service listen on usually?
- Task 3:  FTP sends data in the clear, without any encryption. What acronym is used for a later protocol designed to provide similar functionality to FTP but securely, as an extension of the SSH protocol?
- Task 4:  What is the command we can use to send an ICMP echo request to test our connection to the target?
- Task 5:  From your scans, what version is FTP running on the target?
- Task 6:  From your scans, what OS type is running on the target?
- Task 7:  What is the command we need to run in order to display the 'ftp' client help menu?
- Task 8:  What is username that is used over FTP when you want to log in without having an account?
- Task 9:  What is the response code we get for the FTP message 'Login successful'?
- Task 10: There are a couple of commands we can use to list the files and directories available on the FTP server. One is dir. What is the other that is a common way to list files on a Linux system.
- Task 11: What is the command used to download the file we found on the FTP server?
- Task 12: Submit root flag



### Connect

I always use `openvpn` because it is very easy to connect to the network where the target maschine is.

Just download the `.ovpn` file and type:
 
`sudo openvpn yourfile.ovpn`


**Alternative** :
Use the Pwnbox, which is a preconfigured, browser-based virtual maschine. 

### - Task 1:  What does the 3-letter acronym FTP stand for?

FTP stands for ` File Transfer Protocol`


Answer:
`File Transfer Protocol`



### Task 2:  Which port does the FTP service listen on usually?

Of course we can google, or we use `nmap` to find the open `ftp` port on the target:
```bash
└─$ nmap 10.129.228.24
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-17 16:07 CST
Nmap scan report for 10.129.228.24
Host is up (0.28s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE
21/tcp open  ftp
```

Answer: `21`

### Task 3:  FTP sends data in the clear, without any encryption. What acronym is used for a later protocol designed to provide similar functionality to FTP but securely, as an extension of the SSH protocol?

For `ftp` there is a secure transport `sftp` which literally means `secure file transport protocol`
It is build on SSH for securing the file transfer.

There is also `ftps`which added support for TLS and SSL 

Answer: `SFTP`

### Task 4:  What is the command we can use to send an ICMP echo request to test our connection to the target?

We use that to check if a target answers. 

Answer: `ping`

```bash
PING 10.129.1.17 (10.129.1.17) 56(84) bytes of data.
64 bytes from 10.129.1.17: icmp_seq=1 ttl=63 time=278 ms
64 bytes from 10.129.1.17: icmp_seq=2 ttl=63 time=279 ms
```

### Task 5:  From your scans, what version is FTP running on the target?

We can find out which Service Version is running via `nmap` with the flag `-sV`.

- `-sV` = find the version of the services

```bash
nmap 10.129.228.24 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-17 16:13 CST
Nmap scan report for 10.129.228.24
Host is up (0.28s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
Service Info: OS: Unix
```

Answer: `vsftpd 3.0.3`




### Task 6:  From your scans, what OS type is running on the target?

Also in our `nmap` scan.

Answer: `Unix`

### Task 7:  What is the command we need to run in order to display the 'ftp' client help menu?

As Linux User we know that - we can use `-h` for help.

Answer: `ftp -h`

### Task 8:  What is username that is used over FTP when you want to log in without having an account?

Without account we are `anonymous`

Answer: `anonymous`


### Task 9:  What is the response code we get for the FTP message 'Login successful'?

https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes

Very important to know the "important" ones.

Answer: `230`

### Task 10:  There are a couple of commands we can use to list the files and directories available on the FTP server. One is dir. What is the other that is a common way to list files on a Linux system.?

We use it daily - it is `ls` 

Answer: `ls`

### Task 11:  What is the command used to download the file we found on the FTP server?

A good way to find the anser is with `man ftp`.

```bash

  get remote-file [local-file]
                   Retrieve  the  remote-file  and store it on the local machine.  If the local file name is not specified, it is given the same name it has on the remote machine, subject to alteration by the
                   current case, ntrans, and nmap settings.  The current settings for type, form, mode, and structure are used while transferring the file.

```

Answer: `get`


### Task 12:  Submit root flag

We connect via user `anonymous` and password `anon123`. With `ls`we can see the content of the current folder. There is the `flag.txt`. With `get` we can download the flag to our system.
```bash
ftp> ls
229 Entering Extended Passive Mode (|||25360|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
226 Directory send OK.
ftp> get flag.txt
local: flag.txt remote: flag.txt
229 Entering Extended Passive Mode (|||37843|)
150 Opening BINARY mode data connection for flag.txt (32 bytes).
100% |********************************************************************************************************************************************************************|    32      276.54 KiB/s    00:00 ETA
226 Transfer complete.
32 bytes received in 00:00 (0.11 KiB/s)
ftp> quit
```

and open the flag:

```bash
cat flag.txt
035db21c881520061c53e0536e44f815
```

Answer: `035db21c881520061c53e0536e44f815`