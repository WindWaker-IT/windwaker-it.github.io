---
title: "Bandit: Level 20 - Level 21"
date: 2024-09-11 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 20

**Username:** `bandit20`  
**Password:** `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

### Task:
https://overthewire.org/wargames/bandit/bandit21.html

<!-- PICTURE FROM TASK -->
    There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

    NOTE: Try connecting to your own network daemon to see if it works as you think

    Commands you may need to solve this level
    ssh, nc, cat, bash, screen, tmux, Unix ‘job control’ (bg, fg, jobs, &, CTRL-Z, …)

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit20` on port `2220`.




<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

First lets find out, how to use the program `suconnect`. We try to use it via `./suconnect`.

```bash
bandit20@bandit:~$ ls -l
-rwsr-x--- 1 bandit21 bandit20 15604 Jul 17 15:57 suconnect
bandit20@bandit:~$ ./suconnect
Usage: ./suconnect <portnumber>
This program will connect to the given port on localhost using TCP. If it receives the correct password from the other side, the next password is transmitted back.
```

Okay its not difficult to use. We just have to enter a port number.

For transmitting and making a connection, we use `netcat`.

`nc -nlvp 1234`

- `-n` no DNS resolutions
- `-l` listen mode
- `-v` verbose mode (more details) 
- `-p` port 

And we have to add the password from last level:

`echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" |nc -nlvp 1234`

Also we have to background this command. 

We do that with `bg` and `CTRL+Z`. After putting the `nc` job in the background, we start the `suconnect` with the same port `1234`.


```shell
bandit20@bandit:~$ echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" |nc -nlvp 1234
Listening on 0.0.0.0 1234
^Z
[1]+  Stopped                 echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" | nc -nlvp 1234
bandit20@bandit:~$ jobs
[1]+  Stopped                 echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" | nc -nlvp 1234
bandit20@bandit:~$ bg 1
[1]+ echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" | nc -nlvp 1234 &
bandit20@bandit:~$ ./suconnect 1234
Connection received on 127.0.0.1 47920
Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
Password matches, sending next password
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
```

As you can see, we received the password for the next level!!


Password: `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`


## Explanation: 


### Understanding Jobs in Linux
In Linux, jobs are processes that are running or have been stopped in the background or foreground. You can manage these jobs to run tasks without occupying the terminal.

#### How Jobs Work
Foreground Jobs:

- By default, when you start a process, it runs in the foreground and occupies the terminal until it completes.

Background Jobs:
- You can run processes in the background to free up the terminal by appending `&` to the command or by using bg after stopping a process.