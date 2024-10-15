---
title: "Bandit: Level 30 - Level 31"
date: 2024-10-12 14:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

### Level 30

**Username:** `bandit30`  
**Password:** `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`


### Task:
https://overthewire.org/wargames/bandit/bandit31.html

<!-- PICTURE FROM TASK -->

    There is a git repository at ssh://bandit30-git@localhost/home/bandit30-git/repo via the port 2220. The password for the user bandit30-git is the same as for the user bandit30.

    Clone the repository and find the password for the next level.
<!-- change username bandit!! -->


### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit30` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level


First we create a folder via `mkdir` or `mktemp -d` and go into that directory.

We `clone` the git to our directory:

```bash
bandit30@bandit:~$ cd /tmp/tmp.cYsqMT5RVr
bandit30@bandit:/tmp/tmp.cYsqMT5RVr$ git clone ssh://bandit30-git@localhost:2220/home/bandit30-git/repo .
Cloning into '.'...
```
Lets see what our `README.md` file says today:

```bash

bandit30@bandit:/tmp/tmp.cYsqMT5RVr$ ls
README.md
bandit30@bandit:/tmp/tmp.cYsqMT5RVr$ cat README.md
just an epmty file... muahaha
bandit30@bandit:/tmp/tmp.cYsqMT5RVr$
```

Okay not very useful. Lets check the `log`:

```bash
bandit30@bandit:/tmp/tmp.cYsqMT5RVr$ git log
commit acfc3c67816fc778c4aeb5893299451ca6d65a78 (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Thu Sep 19 07:08:44 2024 +0000

    initial commit of README.md

```

We should consider `commits, tags or branches`. 

There are no hints in `commits` and there is also just one `branch`.

Lets research `tags`:

Git tagging is a method used to identify specific points in a repository's history, such as software release milestones. You can view existing tags with the command git tag. To create a new tag, use git tag -a <tag_name> -m "<tag description/message>". For more information about a particular tag, including the tag message and associated commit, the command git show <tag_name> will display the details.

Alright, if this git has `tags`:

```bash
bandit30@bandit:/tmp/tmp.cYsqMT5RVr/.git$ git tag
secret
bandit30@bandit:/tmp/tmp.cYsqMT5RVr/.git$ git show secret
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
bandit30@bandit:/tmp/tmp.cYsqMT5RVr/.git$
```

Okay we got the password.

Password: `fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`


