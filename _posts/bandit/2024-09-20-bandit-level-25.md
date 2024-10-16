---
title: "Bandit: Level 25 - Level 26"
date: 2024-09-20 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---
<!-- Change LEVELS -->

### Level 25

**Username:** `bandit25`  
**Password:** `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`


### Task:
https://overthewire.org/wargames/bandit/bandit26.html

<!-- PICTURE FROM TASK -->

    Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.

    NOTE: if you’re a Windows user and typically use Powershell to ssh into bandit: Powershell is known to cause issues with the intended solution to this level. You should use command prompt instead.
<!-- change username bandit!! -->


### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit25` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

Interesting level here. Lets have a look what `bandit26` uses as shell. We can check that is our `passwd` file.

```bash
bandit25@bandit:~$ cat /etc/passwd |grep bandit26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
bandit25@bandit:~$
```

It seems, hat `bandit26` uses a script. Lets have a look what in there:

```bash

bandit25@bandit:~$ cat /usr/bin/showtext
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
bandit25@bandit:
```

We have also a private key in our directory. 

```bash
bandit25@bandit:~$ ls
bandit26.sshkey
bandit25@bandit:~$ ssh -i bandit26.sshkey bandit26@localhost -p 2220

  _                     _ _ _   ___   __
 | |                   | (_) | |__ \ / /
 | |__   __ _ _ __   __| |_| |_   ) / /_
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/
Connection to localhost closed.

```

But our connection gets closed automatically. Seems the script does that. We have to find out, what the script does. What is `more`?
```bash
MORE(1)                                                                          User Commands                                                                         MORE(1)

NAME
       more - display the contents of a file in a terminal

SYNOPSIS
       more [options] file ...

DESCRIPTION
       more is a filter for paging through text one screenful at a time. This version is especially primitive. Users should realize that less(1) provides more(1) emulation
       plus extensive enhancements.


```

It seems, that the text from `text.txt` is very short so it is displayed immediately. But we need to bring `more`into `command mode`. We can rescale the terminal window and use `v`to go into vim. (I researched a lot ... Not my favorite level)


Now we are in `vim` and we can press `:` and enter `:set shell=/bin/bash` and after that we can enter `:shell`.

After that we are logged in as `bandit26`and we can retrieve the password.



bandit26@bandit:~$ cat /etc/bandit_pass/bandit26
s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ
bandit26@bandit:~$


Password: `s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`

### Note

Skip that level, if it is "buggy" because I had to use several command lines that the "rescale" works. 