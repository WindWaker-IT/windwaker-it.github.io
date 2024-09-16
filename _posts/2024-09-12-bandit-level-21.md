---
title: "Bandit: Level 21 - Level 22"
date: 2024-09-12 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---
<!-- Change LEVELS -->

### Level 21

**Username:** `bandit21`  
**Password:** `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`

### Task:
https://overthewire.org/wargames/bandit/bandit22.html

<!-- PICTURE FROM TASK -->
    A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

    Commands you may need to solve this level
    cron, crontab, crontab(5) (use “man 5 crontab” to access this)

<!-- change username bandit!! -->

### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit21` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

`Cron` is a tool that can be used in Linux systems to schedule and automate processes. It allows users and administrators to execute tasks at a specific time or within specific intervals.

If you are a windows user, perhaps you know `Scheduled Tasks` which works similar. But `cron` has a unique syntax and way to use.

For example this is how to setup time:
```bash
# System Update
* */6 * * /path/to/update_software.sh

# Execute scripts
0 0 1 * * /path/to/scripts/run_scripts.sh

# Cleanup DB
0 0 * * 0 /path/to/scripts/clean_database.sh

# Backups
0 0 * * 7 /path/to/scripts/backup.sh
```

Okay lets find out, what job runs in /etc/cron.d: 


```bash

bandit21@bandit:~$ cd /etc/cron.d
bandit21@bandit:/etc/cron.d$ ls -la
total 44
drwxr-xr-x   2 root root  4096 Jul 17 15:59 .
drwxr-xr-x 121 root root 12288 Aug  1 14:49 ..
-rw-r--r--   1 root root   120 Jul 17 15:57 cronjob_bandit22
-rw-r--r--   1 root root   122 Jul 17 15:57 cronjob_bandit23
-rw-r--r--   1 root root   120 Jul 17 15:57 cronjob_bandit24
-rw-r--r--   1 root root   201 Apr  8 14:38 e2scrub_all
-rwx------   1 root root    52 Jul 17 15:59 otw-tmp-dir
-rw-r--r--   1 root root   102 Mar 31 00:06 .placeholder
-rw-r--r--   1 root root   396 Jan  9  2024 sysstat
```
Good, we have `read` permissions on the file `cronjob_bandit22`. We can display the content.

```bash

bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```
IF you remember the time pattern. `*` represents a time unit and it means `every unit`. That means the job runs `every minute, every hour and every day`.

After the `time pattern` there is the user, who executes the scheduled task. After that there is the path to the script which will be executed. After that `&> /dev/null` means, that the stdout (output) and stderr (error) will be redirected to `dev/null`, which is the black hole of the device.


Lets have a look at the script `/usr/bin/cronjob_bandit22.sh` 

```bash
cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
What does script?

- First: The script changed the permissions of a directory (`chmod 644` is hexadecimal, 6 = 110 RW;  4 = 100 R)
- Second: it forwards the output from `/etc/bandit_pass/bandit22` to `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv`

And we know from the first line, that we have `read` permissions on that file.

Lets open it:

```bash

bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q

```

And there it is:

Password: `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`



## Explanation: 
(hackthebox - Linux Essentials)

### Understanding Task Scheduling with Cron

With Cron, we can automate the same tasks, but the process for setting up the Cron daemon is a little different than Systemd. To set up the cron daemon, we need to store the tasks in a file called `crontab` and then tell the daemon when to run the tasks. Then we can schedule and automate the tasks by configuring the cron daemon accordingly. The structure of Cron consists of the following components:

| **Time Frame**         | **Description**                                                       |     |
| ---------------------- | --------------------------------------------------------------------- | --- |
| Minutes (0-59)         | This specifies in which minute the task should be executed.           |     |
| Hours (0-23)           | This specifies in which hour the task should be executed.             |     |
| Days of month (1-31)   | This specifies on which day of the month the task should be executed. |     |
| Months (1-12)          | This specifies in which month the task should be executed.            |     |
| Days of the week (0-7) | This specifies on which day of the week the task should be executed.  |     |

```txt
# System Update
* */6 * * /path/to/update_software.sh

# Execute scripts
0 0 1 * * /path/to/scripts/run_scripts.sh

# Cleanup DB
0 0 * * 0 /path/to/scripts/clean_database.sh

# Backups
0 0 * * 7 /path/to/scripts/backup.sh
```