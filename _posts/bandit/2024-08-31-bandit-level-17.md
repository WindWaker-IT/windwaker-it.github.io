---
title: "Bandit: Level 17 - Level 18"
date: 2024-08-30 16:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 17

**Username:** `bandit17`  
**Password:** `EReVavePLFHtFlFsjn3hyzMlvSuSAcRD`

### Task:
https://overthewire.org/wargames/bandit/bandit18.html

<!-- PICTURE FROM TASK -->

    There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

    NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19
<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit17` on port `2220`.

We have several commands available for this task:


    cat (1)              - concatenate files and print on the standard output
    grep (1)             - print lines that match patterns
    ls (1)               - list directory contents
    diff (1)             - compare files line by line


<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

First we need to choose the correct command here. After checking what the available commands do, `diff`seems correct here.


Lets check the manual:

```bash
DIFF(1)                                                                          User Commands                                                                         DIFF(1)

NAME
       diff - compare files line by line

SYNOPSIS
       diff [OPTION]... FILES

DESCRIPTION
       Compare FILES line by line.

       Mandatory arguments to long options are mandatory for short options too.



```

Okay in our case we need to check the changes between password.old and password.new

lets try it:

```bash 
bandit17@bandit:~$ diff passwords.old passwords.new
42c42
< bSrACvJvvBSxEM2SGsV5sn09vc3xgqyp
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO

```
Okay that was quite easy :D - lets try the password, because in the task was written something regarding "bye bye".


```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220

  For support, questions or comments, contact us on discord or IRC.

  Enjoy your stay!

Byebye !
Connection to bandit.labs.overthewire.org closed.
```

Okay password works, but the connection closes. We will check it in the next level.

Password: `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

## Explanation: 

#### `diff` Command

The `diff` command in Linux compares two files line by line and displays the differences between them. It is commonly used to identify changes between two versions of a file. The output shows lines that need to be added, changed, or removed to make the files identical.

#### Example Usage:

```bash
diff file1.txt file2.txt
```