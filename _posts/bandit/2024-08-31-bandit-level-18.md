---
title: "Bandit: Level 18 - Level 19"
date: 2024-08-30 17:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 18

**Username:** `bandit18`  
**Password:** `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

### Task:
https://overthewire.org/wargames/bandit/bandit19.html


<!-- PICTURE FROM TASK -->
    The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

    Commands you may need to solve this level
    ssh, ls, cat
<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit18` on port `2220`.

We have several commands available for this task:

    ssh (1)              - OpenSSH remote login client
    cat (1)              - concatenate files and print on the standard output
    ls (1)               - list directory contents



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

There are several solutions to solve this level. First of all, we have to check what is meant with this `.bashrc`. Lets do a short research.

```
The .bashrc file is a configuration file for the Bash shell. The file consists of commands, functions, aliases, and scripts that execute every time a Bash session starts on Linux or macOS.
```

Okay, so we need to change the file or we try to avoid the bash shell execution.

If we want to avoid it, we can try to give input directly into a `ssh`command.

```bash
root@XXXXX-01:~# ssh bandit18@bandit.labs.overthewire.org -p 2220 ls
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password:
readme

```
As you can see, we could check the directory via `ls`. Lets try to check the content from `readme`.

```
root@XXXXXXX-01:~# ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password:
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8

```
Perfect, we have our password.

Password `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`


## Different way

```bash

root@PREDATOR-01:~# ssh bandit18@bandit.labs.overthewire.org -p 2220 -t /bin/sh
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password:
$ ls
readme
$ cat readme
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8

```

We can also use `-t /bin/sh`!  This is a "pseudo terminal" with `-t` and `/bin/sh` is the target shell. .


## Explanation: 



### What is `/bin/sh`?

`/bin/sh` is a command-line shell on Unix and Linux systems. It's often a symbolic link to a more feature-rich shell, like `bash`. When you execute a command with `/bin/sh`, you’re instructing the system to use this basic shell to interpret your commands.

**Key Points:**
- **Shell:** `/bin/sh` provides a simple, minimal environment to execute commands.
- **Usage:** It’s useful in scripting and system-level tasks where advanced features aren’t necessary.
- **Compatibility:** Since `/bin/sh` is more basic, scripts written for it are often compatible with many different systems.

**In the Context of the Task:**
When you use `/bin/sh`, you’re bypassing the default login shell (`bash` in many cases) and any configurations or scripts that it might automatically execute, such as `.bashrc`. This is helpful when the `.bashrc` file has been tampered with to perform unwanted actions, like logging you out immediately after logging in.

### What is `.bashrc`?

`.bashrc` is a script that runs every time a new terminal session is started in `bash`. This file is usually located in the user’s home directory and contains commands, environment settings, aliases, and functions that customize your shell session.

**Key Points:**
- **Customization:** Users often customize `.bashrc` to set up their environment, such as setting up prompt styles, aliases, or environment variables.
- **Execution:** Every time you start a `bash` session, `.bashrc` is executed automatically.
- **Manipulation:** If someone modifies `.bashrc` with malicious code, it could disrupt your shell session, like logging you out immediately upon login.

**In the Context of the Task:**
In the OverTheWire Bandit level, the `.bashrc` file is configured to log you out as soon as you log in. To avoid this, you can either bypass the execution of `.bashrc` by directly executing commands in the SSH session (e.g., using `/bin/sh`), or use commands like `ls` or `cat` within the SSH command to avoid triggering the `.bashrc` script.

### Summary

By understanding the roles of `/bin/sh` and `.bashrc`, you can better navigate situations where the default shell behavior is undesirable or has been manipulated. Using `/bin/sh` allows you to run commands in a minimal shell environment, bypassing potentially harmful scripts in `.bashrc`.
