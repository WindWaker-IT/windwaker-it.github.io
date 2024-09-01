---
title: "Bandit: Level 6 - Level 7"
date: 2024-08-28 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 6

**Username:** `bandit6`  
**Password:** `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit7.html

    The password for the next level is stored somewhere on the server and has all of the following properties:

    owned by user bandit7
    owned by group bandit6
    33 bytes in size

    Commands you may need to solve this level
    ls , cd , cat , file , du , find , grep

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit6` on port `2220`.

We have several commands available for this task:

- `ls`
- `cd`
- `cat`
- `file`
- `du`
- `find`
<!-- CHANGE COMMANDS IF NECCESSARY -->

### Solve the level

Okay this level is similar to the last level. It seems we have to use the command `find` again but with different flags.
 

To find out how we build this command we go again into the `man` page from `find`.

We search for: (You can search with `/`or `?`)
`-group`
`-user`

We already know `-size` from last level.

```bash

 -user uname
              File is owned by user uname (numeric user ID allowed).

-group gname
              File belongs to group gname (numeric group ID allowed).
```

Okay - very easy.. Just add the specific user and group name behind the flag `-user` and `-group`

Now, how to search in the entire system? 

```shell
To search the current directory, we use the “.” item as the directory path
find . <search_parameter>

To search through your own user folder, use the tilde “~” as the directory path:
find ~ <search_parameter>

You can also search the entire system with the Linux find command. However, due to the large number of files and the possibly deep hierarchy of directories, this can take a long time. To search the entire system, we use a forward slash “/” as the directory path:
find / <search_parameter>

```


Lets build our command:

```bash
find / -user bandit7 -group bandit6 -size 33c
```

You will get a lot of **Permission denied** notifications. How can we avoid that.

In short: 

With adding `2>/dev/null`


```bash

bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
bandit6@bandit:~$

```

And we have our password for the next level:

Password: `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`
 




## Explanation: 

### Understanding Output Redirection in Bash

In Bash, output redirection allows you to control where the output of commands is sent. Here's a brief overview of key concepts:

- **Standard Output (stdout)**: Represented by `1`. It is the default destination for output from commands, typically displayed on the terminal.

- **Standard Error (stderr)**: Represented by `2`. This is used for error messages and diagnostic information. Errors are sent to this stream, which is also usually displayed on the terminal.

- **`/dev/null`**: A special virtual device that discards all data written to it. Essentially, it's a "black hole" for data—anything sent to `/dev/null` is ignored and lost :D .

### Redirecting Output

To redirect output to `/dev/null`, you use the following syntax:

- **`2> /dev/null`**: This command redirects the standard error (stderr) of a command to `/dev/null`. 

  - **`2>`**: Specifies that the redirection applies to the standard error stream (stderr).
  - **`/dev/null`**: The destination where stderr is redirected. Since `/dev/null` discards all input, this effectively means that error messages are suppressed and not shown.

#### Example

If you run a command that might produce error messages and you want to suppress those errors, you can use:

```bash
command 2> /dev/null
```