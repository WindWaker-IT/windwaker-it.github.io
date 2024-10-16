---
title: "Bandit: Level 4 - Level 5"
date: 2024-08-27 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->
### Level 4

**Username:** `bandit4`  
**Password:** `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

### Task:

<!-- PICTURE FROM TASK -->



    The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.

    Commands you may need to solve this level
    ls , cd , cat , file , du , find



<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit4` on port `2220`.

We have several commands available for this task:

- `ls`
- `cd`
- `cat`
- `file`
- `du`
- `find`

<!-- CHANGE COMMANDS IF NECCESSARY -->

### Solve the level

First we check the environment. We move to the folder `inhere` with `cd` and check the content of the folder.
```bash
bandit4@bandit:~/inhere$ cd ..
bandit4@bandit:~$ cd inhere/
bandit4@bandit:~/inhere$ ls -la
total 48
drwxr-xr-x 2 root    root    4096 Jul 17 15:57 .
drwxr-xr-x 3 root    root    4096 Jul 17 15:57 ..
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file00
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file01
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file02
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file03
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file04
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file05
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file06
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file07
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file08
-rw-r----- 1 bandit5 bandit4   33 Jul 17 15:57 -file09
bandit4@bandit:~/inhere$

```

I prefer to use `ls -la` because it lists additional to hidden files also the permissions. We can see 10 files and one of them contents the password for the next level. 

We have to consider, what we want to use, so lets check the commands again.

![Pic2](assets/img/Bandit_1/Bandit_2.png)


The command `file` looks promising. 

Lets give it a try. With `./*` we can tell the command, that it should check **every** file in the current folder.

```bash
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
bandit4@bandit:~/inhere$
```

We can see, that `file07` contains an ASCII text. Lets check this file.

```bash

bandit4@bandit:~/inhere$ cat ./-file07
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
bandit4@bandit:~/inhere$

```

Perfect - we have the password for the new level.

Password: `4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

## Explanation: 

The `file` command is useful for identifying what type of content a file contains, 
such as `ELF`, `Perl script`, `ASCII text`, or `data`. 
In this context, we're specifically interested in files that are human-readable, 
typically encoded in ASCII or Unicode. 
For instance, `ELF` files aren't readable by humans—if you attempt to display 
their content, you'd see a series of random, unreadable characters.

When you need to apply a command to all files in a directory, 
manually typing each filename is impractical. 
Instead, you can use the wildcard `*`, which represents any sequence of characters. 
This allows you to run a command on multiple files simultaneously. 
For example, `file*` would match files such as `file00`, `file`, and `fileXYZ`.






<!--COPY UNTIL HERE  -->


