---
title: "Bandit: Level 22 - Level 23"
date: 2024-09-12 13:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---


<!-- Change LEVELS -->

### Level 22

**Username:** `bandit22`  
**Password:** `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`

### Task:
https://overthewire.org/wargames/bandit/bandit23.html

<!-- PICTURE FROM TASK -->
    A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

    NOTE: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.

    Commands you may need to solve this level
    cron, crontab, crontab(5) (use “man 5 crontab” to access this)
<!-- change username bandit!! -->

### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit22` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

Like the last level lets find out what the `cron` job does.


```bash
cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```
Okay for this level we need to be able to read what this script does:

- Variable `myname`is the logged user (whoami)
- Variable `mytarget`has some text and adds `myname` to it and makes a md5 hash out of it. At the end it cuts parts
- Afterwars it writes the path with the logged user `myname` and the path from `mytarget`
- the last line puts the output of the file with variable `myname` to a path in /`tmp` with `mytarget`


#### Conclusion

After checking the script it is clear, that we have to change the variable `myname` because we want to know what the password is from `bandit23` not us (`bandit22`). 

The target is to know, where the `cron job` wrote the password. So we need to find the correct path. 

Lets change the `myname="bandit23"` and execute it.

```bash

bandit22@bandit:/etc/cron.d$ mktemp -d
/tmp/tmp.mCDIokSFiw
bandit22@bandit:/etc/cron.d$ cp /usr/bin/cronjob_bandit23.sh /tmp/tmp.mCDIokSFiw
bandit22@bandit:/etc/cron.d$ cd /tmp/tmp.mCDIokSFiw
bandit22@bandit:/tmp/tmp.mCDIokSFiw$ nano cronjob_bandit23.sh
bandit22@bandit:/tmp/tmp.mCDIokSFiw$ chmod +x cronjob_bandit23.sh
bandit22@bandit:/tmp/tmp.mCDIokSFiw$ ./cronjob_bandit23.sh
Copying passwordfile /etc/bandit_pass/bandit23 to /tmp/8ca319486bfbbc3663ea0fbe81326349
bandit22@bandit:/tmp/tmp.mCDIokSFiw$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

Very good, we found out the password just by changing the value of one variable.

Password: `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`



### Note:

If you could not read the script or had difficulties on that level I would recommend to learn the `basics` from `bash scripting`. It is relatively easy to learn and very useful.