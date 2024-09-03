---
title: "Bandit: Level 19 - Level 20"
date: 2024-08-30 18:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 19

**Username:** `bandit19`  
**Password:** `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

### Task:
https://overthewire.org/wargames/bandit/bandit20.html

<!-- PICTURE FROM TASK -->
    To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

    Helpful Reading Material
    setuid on Wikipedia
<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit19` on port `2220`.




<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

We dig into **Linux Permissions** more specifically the **setuid**. Basically it means, if it is set on a file or executable, than it runs with the privileges of the file`s owner rather than the user who executes it. This is useful for example if user need to use program which need administrative permissions. 


Lets check the permissions on the file bandit20.
```bash
bandit19@bandit:~$ ls -l
total 16
-rwsr-x--- 1 bandit20 bandit19 14880 Jul 17 15:57 bandit20-do
```
Okay we can see the `setuid`is set with the `s` in `rwsr`. The owner is **bandit20** and the group is **bandit19**. 

**Conclusion**:
the user bandit19 can execute the binary, but the binary is executed as user bandit20.

Lets try what happens if we open the file:
```
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do id
bandit19@bandit:~$ ./bandit20-do id
uid=11019(bandit19) gid=11019(bandit19) euid=11020(bandit20) groups=11019(bandit19)
bandit19@bandit:~$
```
That means, we can access the users password file, as user bandit20.

```bash

bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO

```

Perfect we did it.


Password: `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

## Explanation: 


### Understanding Setuid in Linux

In Linux, **setuid** (Set User ID) is a special type of file permission that allows a user to run an executable with the permissions of the file's owner, rather than with the permissions of the user who is running the file. This is particularly useful for tasks that require higher privileges than those of the user executing the file.

---

#### How Setuid Works

- **Normal Permissions**: 
  - When you execute a file, it typically runs with your user permissions.
  - For example, if you don't have permission to read a certain file, you can't read it just by running a program.

- **Setuid Permission**: 
  - If a file has the setuid bit set, it runs with the permissions of the file's owner.
  - This means that if the file is owned by the root user and has the setuid bit set, it will run with root privileges, even if a regular user executes it.

---

#### Identifying Setuid Files

- You can identify files with setuid permissions by looking at the file's permissions using the `ls -l` command:
  ```bash
  ls -l /path/to/file
  ```

