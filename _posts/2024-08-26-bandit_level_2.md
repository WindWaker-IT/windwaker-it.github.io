---
title: "Bandit: Level 2 - Level 3"
date: 2024-08-24 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->
## Bandit: Level 2 - Level 3
### Level 2

**Username:** `bandit2`  
**Password:** `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit3.html

![Task](assets\img\Bandit_2\Bandit_2_1.png "Task")

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit2` on port `2220`.

We have several commands available for this task:

- `ls`
- `cd`
- `cat`
- `file`
- `du`
- `find`
<!-- CHANGE COMMANDS IF NECCESSARY -->

### Solve the level

This level is very similar to the last one. Trying the filename does not work.

```bash

bandit2@bandit:~$ cat spaces in this filename
cat: spaces: No such file or directory
cat: in: No such file or directory
cat: this: No such file or directory
cat: filename: No such file or directory
bandit2@bandit:~$ cat "spaces in this filename"
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
bandit2@bandit:~$ cat ./spaces\ in\ this\ filename
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
bandit2@bandit:~$

```

The reason for that is, that we try to find four files with `spaces in this filename`, which are not existing of course :smiley: 

So we need `""`to surround the names with quotes to tell the computer, that all of it belongs to the name. 

 Other possibility is wo work with `/` and `\`  which also work here.


The password here is: `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`


## Explanation: 

In Linux and other Unix-like systems, it's generally advised **not** to include spaces in filenames or directory names. The reason is that spaces in command lines are usually interpreted as **delimiters**, which separate different commands or arguments.

For example, if you try to use the `cat` command on a file with spaces in its name, the system will treat each word in the filename as a separate file. This can lead to errors and confusion.

To avoid this, it's common practice to use **underscores** (`_`) or **dashes** (`-`) instead of spaces when naming files or directories.


<!--COPY UNTIL HERE  -->




