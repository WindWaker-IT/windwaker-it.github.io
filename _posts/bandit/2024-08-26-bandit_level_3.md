---
title: "Bandit: Level 3 - Level 4"
date: 2024-08-26 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->
## Bandit: Level 3 - Level 4
### Level 3

**Username:** `bandit3`  
**Password:** `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit4.html

![Task](assets/img/Bandit_3/Bandit_3_1.png "Task")

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit3` on port `2220`.

We have several commands available for this task:

- `ls`
- `cd`
- `cat`
- `file`
- `du`
- `find`
<!-- CHANGE COMMANDS IF NECCESSARY -->

### Solve the level

First lets check the environment. With `ls` we see the mentioned folder *inhere*. 

Lets see what is in the folder:

```bash
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ cd inhere/
bandit3@bandit:~/inhere$ ls
bandit3@bandit:~/inhere$
```

Okay, we changed the directory with `cd` but unfortunately it seems we dont see the hidden file via `ls`. Lets check the manual or help:

```bash
man ls 
ls --help
```

After short studying - there is the parameter `-a`.

```bash
bandit3@bandit:~/inhere$ ls --help
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.

Mandatory arguments to long options are mandatory for short options too.
  -a, --all                  do not ignore entries starting with .

  ```

Lets try it:

```bash

bandit3@bandit:~/inhere$ ls -a
.  ..  ...Hiding-From-You
bandit3@bandit:~/inhere$ cat ...Hiding-From-You
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
bandit3@bandit:~/inhere$


``` 

Perfect, we have our password for the next level:

`2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`





## Explanation: 

In Unix-like operating systems, files and directories that begin with a dot (`.`) are considered hidden. These files are not displayed by default when using the `ls` command, which lists files in a directory. 


<!--COPY UNTIL HERE  -->




