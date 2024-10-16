---
title: "Bandit: Level 29 - Level 30"
date: 2024-10-12 13:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

### Level 29

**Username:** `bandit29`  
**Password:** `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`


### Task:
https://overthewire.org/wargames/bandit/bandit30.html

<!-- PICTURE FROM TASK -->

    There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo via the port 2220. The password for the user bandit29-git is the same as for the user bandit29.

    Clone the repository and find the password for the next level.

<!-- change username bandit!! -->


### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit29` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

First we create a folder via `mkdir` or `mktemp -d` and go into that directory.

We `clone` the git to our directory:

```bash

bandit29@bandit:/tmp/tmp.EkHEOwDRrc$ git clone ssh://bandit29-git@localhost:2220/home/bandit29-git/repo
Cloning into 'repo'...
```

Now we check the `README.md` file:

```bash

bandit29@bandit:/tmp/tmp.EkHEOwDRrc/repo$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: <no passwords in production!>


```

Okay `no password in production`sound a little bit like a different department / branch.

Lets check the `log`:

```bash
bandit29@bandit:/tmp/tmp.EkHEOwDRrc/repo$ git log
commit 6ac7796430c0f39290a0e29a4d32e5126544b022 (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Thu Sep 19 07:08:41 2024 +0000

    fix username

commit e65a928cca4db1863b478cf5e93d1d5b1c1bd6b2
Author: Ben Dover <noone@overthewire.org>
Date:   Thu Sep 19 07:08:41 2024 +0000

    initial commit of README.md

```

Okay not very helpful, lets see the commits:


```bash
bandit29@bandit:/tmp/tmp.EkHEOwDRrc/repo$ git show
commit 6ac7796430c0f39290a0e29a4d32e5126544b022 (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Thu Sep 19 07:08:41 2024 +0000

    fix username

diff --git a/README.md b/README.md
index 2da2f39..1af21d3 100644
--- a/README.md
+++ b/README.md
@@ -3,6 +3,6 @@ Some notes for bandit30 of bandit.

 ## credentials

-- username: bandit29
+- username: bandit30
 - password: <no passwords in production!>
```
`diff --git a/README.md b/README.md` sounds that there are other branches:

Lets find out how we can list all branches:

```bash
Specific git-branch actions:
    -a, --all             list both remote-tracking and local branches
```

Lets try it:

```bash
bandit29@bandit:/tmp/tmp.EkHEOwDRrc/repo$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```

Okay we have a `dev` branch. I think `dev` could be also `Production`. Lets change the branch and check if something is there:

```bash

bandit29@bandit:/tmp/tmp.EkHEOwDRrc/repo$ git checkout dev
branch 'dev' set up to track 'origin/dev'.
Switched to a new branch 'dev'
bandit29@bandit:/tmp/tmp.EkHEOwDRrc/repo$ ls
code  README.md

```

Okay we are into the `dev`branch now. Lets see what is written in this `README.md`

```bash
bandit29@bandit:/tmp/tmp.EkHEOwDRrc/repo$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL


```

Good, we got it.

Password: `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZ`
