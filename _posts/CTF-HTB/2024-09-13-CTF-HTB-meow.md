---
title: "[CTF] HackTheBox - Meow (Basics)"
date: 2024-09-13 12:00:00 -500 
categories: [CTF - HackTheBox, Meow (Basics)]
tags: [ctf,exploit,flags,htb,hackthebox]
author: Christoph K
---


Tasks:

- Connect
- Task 1:  What does the acronym VM stand for?
- Task 2:  What tool do we use to interact with the operating system in order to issue commands via the command line, such as the one to start our VPN connection? It's also known as a console or shell.
- Task 3:  What service do we use to form our VPN connection into HTB labs?
- Task 4:  What tool do we use to test our connection to the target with an ICMP echo request?
- Task 5:  What is the name of the most common tool for finding open ports on a target?
- Task 6:  What service do we identify on port 23/tcp during our scans?
- Task 7:  What username is able to log into the target over telnet with a blank password?
- Task 8:  Submit root flag



### Connect

I always use `openvpn` because it is very easy to connect to the network where the target maschine is.

Just download the `.ovpn` file and type:
 
`sudo openvpn yourfile.ovpn`


**Alternative** :
Use the Pwnbox, which is a preconfigured, browser-based virtual maschine. 

### - Task 1:  What does the acronym VM stand for?

We should know this :-) 

Answer:
`Virtual Maschine`



### Task 2:  What tool do we use to interact with the operating system in order to issue commands via the command line, such as the one to start our VPN connection? It's also known as a console or shell.


Also very easy.

Answer: `Terminal`

### Task 3:  What service do we use to form our VPN connection into HTB labs?

Already in the `Connect` Section - I use openvpn.

Answer: `openvpn`

### Task 4:  What tool do we use to test our connection to the target with an ICMP echo request?

We use that to check if a target answers. 

Answer: `ping`

```bash
PING 10.129.1.17 (10.129.1.17) 56(84) bytes of data.
64 bytes from 10.129.1.17: icmp_seq=1 ttl=63 time=278 ms
64 bytes from 10.129.1.17: icmp_seq=2 ttl=63 time=279 ms
```

### Task 5:  What is the name of the most common tool for finding open ports on a target?

To find open ports and services in a network we use `nmap`.

Answer: `nmap`

### Task 6:  What service do we identify on port 23/tcp during our scans?

I always remember it like that.`SSH` is port 22 and we use that very often. To open a TCP Stream, I use `telnet` which is port 23.

Answer: `telnet`

### Task 7:  What username is able to log into the target over telnet with a blank password?

As Linux User we know that ofc. 

Answer: `root`

### Task 8:  Submit root flag

```bash
root@Meow:~# cd /root
root@Meow:~# ls
flag.txt  snap
root@Meow:~# cat flag.txt
b40abdfe23665f766f9c61ecba8a4c19

```

Answer: `b40abdfe23665f766f9c61ecba8a4c19`


Note:
the root flag is most of the times in the folder `/root/`.

