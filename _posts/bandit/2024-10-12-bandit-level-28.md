---
title: "Bandit: Level 28 - Level 29"
date: 2024-10-12 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

### Level 28

**Username:** `bandit28`  
**Password:** `Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`


### Task:
https://overthewire.org/wargames/bandit/bandit29.html

<!-- PICTURE FROM TASK -->

    There is a git repository at ssh://bandit28-git@localhost/home/bandit28-git/repo via the port 2220. The password for the user bandit28-git is the same as for the user bandit28.

    Clone the repository and find the password for the next level.

<!-- change username bandit!! -->


### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit28` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level


First we need a place to clone our `git`. We create a directory via `mkdir`:

```bash
bandit28@bandit:/tmp$ mkdir gittest_lvl28
bandit28@bandit:/tmp$ cd gittest_lvl28
```

Now we clone the git with `port 2220`. 


```bash
bandit28@bandit:/tmp/gittest_lvl28$ git clone ssh://bandit28-git@localhost:2220/home/bandit28-git/repo .
Cloning into '.'...
``` 

We have a `README.md`file. Lets read the file:

```bash
bandit28@bandit:/tmp/gittest_lvl28$ cat README.md
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx

```

Okay the password is not readable. What we can do is to check the `git logs` and the `commits`. That shows us previous changes:

```bash
bandit28@bandit:/tmp/gittest_lvl28/.git$ git log
commit 817e303aa6c2b207ea043c7bba1bb7575dc4ea73 (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Thu Sep 19 07:08:39 2024 +0000

    fix info leak

commit 3621de89d8eac9d3b64302bfb2dc67e9a566decd
Author: Morla Porla <morla@overthewire.org>
Date:   Thu Sep 19 07:08:39 2024 +0000

    add missing data

commit 0622b73250502618babac3d174724bb303c32182
Author: Ben Dover <noone@overthewire.org>
Date:   Thu Sep 19 07:08:39 2024 +0000

    initial commit of README.md

```

As we can see, there were made some changes. Probably `fix info leak` made our password unreadable.

But we can see the changes via `git show`:


```bash
bandit28@bandit:/tmp/gittest_lvl28/.git$ git show
commit 817e303aa6c2b207ea043c7bba1bb7575dc4ea73 (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Thu Sep 19 07:08:39 2024 +0000

    fix info leak

diff --git a/README.md b/README.md
index d4e3b74..5c6457b 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials

 - username: bandit29
-- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
+- password: xxxxxxxxxx

```

You can see with `--` and `+-` which parts of project were edited.

Password: `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`

