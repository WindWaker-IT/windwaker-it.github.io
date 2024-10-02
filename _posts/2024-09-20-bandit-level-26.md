---
title: "Bandit: Level 26 - Level 27"
date: 2024-09-20 13:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---
<!-- Change LEVELS -->

### Level 26

**Username:** `bandit26`  
**Password:** `s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`


### Task:
https://overthewire.org/wargames/bandit/bandit27.html

<!-- PICTURE FROM TASK -->
    Good job getting a shell! Now hurry and grab the password for bandit27!
<!-- change username bandit!! -->


### Solve the level

Please repeat the steps from `Level 25` to get a shell with user `Bandit26`.


Lets see what we have here.
```bash
bandit26@bandit:~$ ls
bandit27-do  text.txt
bandit26@bandit:~$ ls
bandit27-do  text.txt
bandit26@bandit:~$ cat text.txt
  _                     _ _ _   ___   __
 | |                   | (_) | |__ \ / /
 | |__   __ _ _ __   __| |_| |_   ) / /_
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/
bandit26@bandit:~$ ls
bandit27-do  text.txt
bandit26@bandit:~$ ls -la
total 44
drwxr-xr-x  3 root     root      4096 Sep 19 07:08 .
drwxr-xr-x 70 root     root      4096 Sep 19 07:09 ..
-rwsr-x---  1 bandit27 bandit26 14880 Sep 19 07:08 bandit27-do
-rw-r--r--  1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--  1 root     root      3771 Mar 31  2024 .bashrc
-rw-r--r--  1 root     root       807 Mar 31  2024 .profile
drwxr-xr-x  2 root     root      4096 Sep 19 07:08 .ssh
-rw-r-----  1 bandit26 bandit26   258 Sep 19 07:08 text.txt

```

Okay we have again a `suid`. We can just use this again to get the password.




```bash

bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
bandit26@bandit:~$
```


Finished.

Password: `upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB`