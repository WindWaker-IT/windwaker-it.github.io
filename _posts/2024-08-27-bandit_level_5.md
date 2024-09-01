---
title: "Bandit: Level 5 - Level 6"
date: 2024-08-28 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 5

**Username:** `bandit5`  
**Password:** `4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit6.html

 
    The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

    human-readable
    1033 bytes in size
    not executable
    Commands you may need to solve this level
    ls , cd , cat , file , du , find

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit5` on port `2220`.

We have several commands available for this task:

- `ls`
- `cd`
- `cat`
- `file`
- `du`
- `find`
<!-- CHANGE COMMANDS IF NECCESSARY -->

### Solve the level

We begin again to move to the directory `inhere` and check with `ls -la` the contents:

```bash

bandit5@bandit:~/inhere$ ls -la
total 88
drwxr-x--- 22 root bandit5 4096 Jul 17 15:57 .
drwxr-xr-x  3 root root    4096 Jul 17 15:57 ..
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere00
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere01
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere02
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere03
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere04
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere05
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere06
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere07
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere08
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere09
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere10
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere11
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere12
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere13
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere14
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere15
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere16
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere17
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere18
drwxr-x---  2 root bandit5 4096 Jul 17 15:57 maybehere19
bandit5@bandit:~/inhere$

```
Okay, we have to find a file which has **1033 bytes** and is **not executable**. Here the command `find` should come handy. Lets check the manual for the specific needs.

```bash
man find
find --help
```

It is tricky to find the correct parameters but we get to this. Lets start with the **size** 


```bash

 -size n[cwbkMG]
              File uses less than, more than or exactly n units of space, rounding up.  The following suffixes can be used:

              `b'    for 512-byte blocks (this is the default if no suffix is used)

              `c'    for bytes

              `w'    for two-byte words

              `k'    for kibibytes (KiB, units of 1024 bytes)

              `M'    for mebibytes (MiB, units of 1024 * 1024 = 1048576 bytes)

              `G'    for gibibytes (GiB, units of 1024 * 1024 * 1024 = 1073741824 bytes)

```

That means, we can use Parameter `-size 1033c` 

Next we need to find a file, which is **not executable**.

```bash

-executable
              Matches  files which are executable and directories which are searchable (in a file name resolution sense) by the current user.  This takes into ac‐
              count access control lists and other permissions artefacts which the -perm test ignores.  This test makes use of the access(2) system call,  and  so
              can  be  fooled by NFS servers which do UID mapping (or root-squashing), since many systems implement access(2) in the client's kernel and so cannot
              make use of the UID mapping information held on the server.  Because this test is based only on the result of the access(2) system call, there is no
              guarantee that a file for which this test succeeds can actually be executed.

``` 

Okay, and because we want **NOT** executable we neet to use `!`before `executiable`


And we need to make sure, that it is a file, what we are searching.

```bash

 -type c
              File is of type c:

              b      block (buffered) special

              c      character (unbuffered) special

              d      directory

              p      named pipe (FIFO)

              f      regular file

              l      symbolic link; this is never true if the -L option or the -follow option is in effect, unless the symbolic link is broken.  If  you  want  to
                     search for symbolic links when -L is in effect, use -xtype.

              s      socket

              D      door (Solaris)
```


Perfect, we need also `-type -f`


Lets put together the command:


```shell
bandit5@bandit:~/inhere$ find -type f -size 1033c ! -executable
./maybehere07/.file2
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG

``` 


Here is the password for the next level:


Password: `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`



## Explanation: 



To locate files based on specific criteria, we use the `find` command in Linux with various options. Here’s what each option does:

- **`-type f`**: This option tells `find` to look for **regular files** only. It excludes directories, symbolic links, and other file types. The `f` stands for "file," and this ensures that the command does not return directories or other non-file types.

- **`-size 1033c`**: This option specifies the size of the files to search for. The `-size` parameter allows you to define the size in various units:
  - `c` stands for bytes.
  - In this case, `1033c` means the command will look for files that are exactly **1033 bytes** in size. You can also use other suffixes like `k` for kibibytes or `M` for mebibytes if needed.

- **`! -executable`**: This option is used to find files that are **not executable**. The `-executable` flag matches files that the current user can execute. By using `!` before `-executable`, you negate this condition, so the command returns files that are not marked as executable. The `!` operator is a negation operator, and it inverts the condition that follows it.





