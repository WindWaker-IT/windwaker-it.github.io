---
title: "Bandit: Level 27 - Level 28"
date: 2024-09-20 14:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---
<!-- Change LEVELS -->

### Level 27

**Username:** `bandit27`  
**Password:** `upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB`


### Task:
https://overthewire.org/wargames/bandit/bandit28.html

<!-- PICTURE FROM TASK -->

    There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo via the port 2220. The password for the user bandit27-git is the same as for the user bandit27.

    Clone the repository and find the password for the next level.

<!-- change username bandit!! -->


### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit27` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

First of all, what is `git` and what can we do with it.

```bash

GIT(1)                                                                                           Git Manual                                                                                          GIT(1)

NAME
       git - the stupid content tracker

SYNOPSIS
       git [-v | --version] [-h | --help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p|--paginate|-P|--no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           [--config-env=<name>=<envvar>] <command> [<args>]

DESCRIPTION
       Git is a fast, scalable, distributed revision control system with an unusually rich command set that provides both high-level operations and full access to internals.

       See gittutorial(7) to get started, then see giteveryday(7) for a useful minimum set of commands. The Git User’s Manual[1] has a more in-depth introduction.

       After you mastered the basic concepts, you can come back to this page to learn what commands Git offers. You can learn more about individual Git commands with "git help command". gitcli(7) manual
       page gives you an overview of the command-line command syntax.

       A formatted and hyperlinked copy of the latest Git documentation can be viewed at https://git.github.io/htmldocs/git.html or https://git-scm.com/docs.

```

So our task is to `clone` the repository from an address to our device. Lets to that with `clone`. But first we need a new folder for, a target for our cloned  repository.

```bash

bandit27@bandit:~$ mktemp -d
/tmp/tmp.hUdGDNVgsP
bandit27@bandit:~$ cd /tmp/tmp.hUdGDNVgsP
```


Now we can clone. Note the `Port` is behind the `SSH Address`.


```bash

bandit27@bandit:/tmp/tmp.hUdGDNVgsP$ git clone ssh://bandit27-git@localhost/home/bandit27-git/repo .
Cloning into '.'...

```

We use the current password as pasword. We use the `.` to copy into the current directory.

Lets see what we cloned here: 

```bash

bandit27@bandit:/tmp/tmp.hUdGDNVgsP$ ls
README
bandit27@bandit:/tmp/tmp.hUdGDNVgsP$ cat README
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
bandit27@bandit:/tmp/tmp.hUdGDNVgsP$

```

Perfect. We did our first clone from a github repository.

Password: `Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`