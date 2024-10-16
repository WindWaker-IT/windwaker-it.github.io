---
title: "Bandit: Level 16 - Level 17"
date: 2024-08-30 15:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 16

**Username:** `bandit16`  
**Password:** `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit17.html

    The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

    Helpful note: Getting “DONE”, “RENEGOTIATING” or “KEYUPDATE”? Read the “CONNECTED COMMANDS” section in the manpage.

    Commands you may need to solve this level
    ssh, telnet, nc, ncat, socat, openssl, s_client, nmap, netstat, ss

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit16` on port `2220`.

We have several commands available for this task:


    ssh (1)              - OpenSSH remote login client
    telnet (1)           - user interface to the TELNET protocol
    nc (1)               - arbitrary TCP and UDP connections and listens
    openssl (1ssl)       - OpenSSL command line program
    s_client (1ssl)      - OpenSSL application commands
    nmap (1)             - Network exploration tool and security / port scanner


<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

For this level we have to find the correct port which speaks encrypted and then connect to it. 

And now the command `nmap` comes handy, because we have to explore our network and ports.

Lets check the manual.

```bash

NMAP(1)                                                                      Nmap Reference Guide                                                                      NMAP(1)

NAME
       nmap - Network exploration tool and security / port scanner

SYNOPSIS

       nmap [Scan Type...] [Options] {target specification}

           SCAN TECHNIQUES:
             -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
             -sU: UDP Scan
             -sN/sF/sX: TCP Null, FIN, and Xmas scans
             --scanflags <flags>: Customize TCP scan flags
             -sI <zombie host[:probeport]>: Idle scan
             -sY/sZ: SCTP INIT/COOKIE-ECHO scans
             -sO: IP protocol scan
             -b <FTP relay host>: FTP bounce scan
           PORT SPECIFICATION AND SCAN ORDER:
             -p <port ranges>: Only scan specified ports
               Ex: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9
             --exclude-ports <port ranges>: Exclude the specified ports from scanning
             -F: Fast mode - Scan fewer ports than the default scan
             -r: Scan ports sequentially - don't randomize
             --top-ports <number>: Scan <number> most common ports
             --port-ratio <ratio>: Scan ports more common than <ratio>
           SERVICE/VERSION DETECTION:
             -sV: Probe open ports to determine service/version info

```
A bit searching is necessary but with this information we can build our command:

```bash
bandit16@bandit:~$ nmap localhost -p 31000-32000 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-03 08:26 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00031s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
```

Good. We have two ports which are using SSL. One has an unknown service, which could be promising. Lets try to speak with the port `31790`.

```bash
bandit16@bandit:~$ openssl s_client localhost:31790

```

Enter the password from the current level and you should get an output from another private key. On my client that didn`t work properly (It is a bug) so I used following command:

```bash
bandit16@bandit:~$ echo kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx | openssl s_client -connect localhost:31790 -quiet
Can't use SSL_get_servername
depth=0 CN = SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = SnakeOil
verify return:1
Correct!
-----BEGIN RSA PRIVATE KEY-----
....
....
....

```

We have to save this private key for connecting via ssh to the next level.
```bash

bandit16@bandit:~$ cd /tmp/tmp.GDTdLNFjpQ
bandit16@bandit:/tmp/tmp.GDTdLNFjpQ$ touch priv_key.key
bandit16@bandit:/tmp/tmp.GDTdLNFjpQ$ nano priv_key.key
```
I saved the content of the private key into my created file. Lets try to connect:

```bash
bandit16@bandit:/tmp/tmp.GDTdLNFjpQ$ ssh bandit17@localhost -i priv_key.key
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0664 for 'priv_key.key' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "priv_key.key": bad permissions
bandit17@localhost: Permission denied (publickey).
```
Okay we need to change the permissions of this file. Check the current permissions:
```bash
bandit16@bandit:/tmp/tmp.GDTdLNFjpQ$ ls -la
total 10816
drwx------    2 bandit16 bandit16     4096 Sep  3 08:44 .
drwxrwx-wt 2497 root     root     11063296 Sep  3 08:45 ..
-rw-rw-r--    1 bandit16 bandit16     1675 Sep  3 08:44 priv_key.key
```

there are 9 letters in front of the file.

`-rw rw r--` the first 3 is User, 4-6 is group and 7-9 is other.
`-rw-rw-r--    1 bandit16 bandit16` the first name means `owner`and the second one means `group`.

Lets change the permssions here with `chmod`:
We delete the permissions: "r = read; w = write" from "g=group" with `-`
```bash
bandit16@bandit:/tmp/tmp.GDTdLNFjpQ$ chmod g-rw priv_key.key
```

Then we delete "r=read" from "o=other"
```
bandit16@bandit:/tmp/tmp.GDTdLNFjpQ$ chmod o-r priv_key.key
```

lets try again:

```bash

bandit16@bandit:/tmp/tmp.GDTdLNFjpQ$ ssh bandit17@localhost -i priv_key.key -p 2220
bandit17@bandit:~$
```

Perfect it worked. Lets find the password from Level 17.

We know that every password from the current level is stored in `/etc/bandit_pass/LEVEL`

```bash
bandit17@bandit:/etc/bandit_pass$ cat bandit17
EReVavePLFHtFlFsjn3hyzMlvSuSAcRD
```

Perfect, we got our password.

Password: `EReVavePLFHtFlFsjn3hyzMlvSuSAcRD`



## Explanation: 

#### 1. Nmap (`nmap`)

`nmap` (Network Mapper) is an open-source tool used for network discovery and security auditing. It is widely used for discovering hosts and services on a computer network, thereby creating a map of the network. `nmap` can be used to detect open ports, identify running services, and determine the operating systems of the devices on the network. It's a valuable tool for network administrators and security professionals for assessing network security and diagnosing network issues.


#### Chmod (`chmod`)

`chmod` (change mode) is a command used in Unix and Unix-like operating systems to change the file system modes or permissions of files and directories. The permissions define who can read, write, or execute a file or directory. This command allows users to modify these permissions to ensure the appropriate level of access.

#### Permission Types

- **`r` (read):** Allows the reading of the file.
- **`w` (write):** Allows modifying the file.
- **`x` (execute):** Allows executing the file or entering a directory.

#### Numeric Mode

Permissions can be set using numeric values where each digit represents the permissions for the owner, group, and others.

- **`7`** - Read, write, and execute (`rwx`)
- **`6`** - Read and write (`rw-`)
- **`5`** - Read and execute (`r-x`)
- **`4`** - Read only (`r--`)
- **`3`** - Write and execute (`wx-`)
- **`2`** - Write only (`w--`)
- **`1`** - Execute only (`--x`)
- **`0`** - No permissions (`---`)


