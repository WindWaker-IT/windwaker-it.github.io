---
title: "Bandit: Level 31 - Level 32"
date: 2024-10-14 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

### Level 31

**Username:** `bandit31`  
**Password:** `fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`

### Task:
https://overthewire.org/wargames/bandit/bandit32.html

<!-- PICTURE FROM TASK -->

    There is a git repository at ssh://bandit31-git@localhost/home/bandit31-git/repo via the port 2220. The password for the user bandit31-git is the same as for the user bandit31.
<!-- change username bandit!! -->


### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit31` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

We create again a temporary folder and clone the required respository with `git clone`.

```bash
bandit31@bandit:~$ mktemp -d
/tmp/tmp.RrcXNJfTmn
bandit31@bandit:~$ cd /tmp/tmp.RrcXNJfTmn
bandit31@bandit:/tmp/tmp.RrcXNJfTmn$ git clone ssh://bandit31-git@localhost:2220/home/bandit31-git/repo
Cloning into 'repo'...
```

Lets read the `README.md`:

```bash

bandit31@bandit:/tmp/tmp.RrcXNJfTmn/repo$ cat README.md
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master

``` 

Okay we create the file `key.txt` and enter the content `May I come in?`.

Lets check the status via `git status`:

```bash
bandit31@bandit:/tmp/tmp.RrcXNJfTmn/repo$ git status
On branch master
Your branch is up to date with 'origin/master'.

```

Strange though.. We can use `git commit -a` to get access to the commitments or we just check the `.gitignore` file.

```bash
bandit31@bandit:/tmp/tmp.RrcXNJfTmn/repo$ cat .gitignore
*.txt
```

Okay this is the problem. Just delete the content of this file and it should work.

```bash
bandit31@bandit:/tmp/tmp.RrcXNJfTmn/repo$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   .gitignore

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        key.txt
```

Lets delete the `#` from our commitment list and push the changes via `git push`:


```bash
bandit31@bandit:/tmp/tmp.RrcXNJfTmn/repo$ git commit -a
Unable to create directory /home/bandit31/.local/share/nano/: No such file or directory
It is required for saving/loading search history or cursor positions.

[master 478ae9b]        new file:   key.txt
 2 files changed, 1 insertion(+), 1 deletion(-)
 create mode 100644 key.txt
bandit31@bandit:/tmp/tmp.RrcXNJfTmn/repo$ git push
The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit31/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit31/.ssh/known_hosts).
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

bandit31-git@localhost's password:
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 2 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (4/4), 340 bytes | 340.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
remote: ### Attempting to validate files... ####
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
To ssh://localhost:2220/home/bandit31-git/repo
 ! [remote rejected] master -> master (pre-receive hook declined)

 ```

 And we got our password:

 Password: `3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`