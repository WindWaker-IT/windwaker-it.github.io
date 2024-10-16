---
title: "Bandit: Level 14 - Level 15"
date: 2024-08-30 14:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 14

**Username:** `bandit14`  
**Password:** `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit15.html

    The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

    Commands you may need to solve this level
    ssh, telnet, nc, openssl, s_client, nmap

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit14` on port `2220`.

We have several commands available for this task:


    ssh (1)              - OpenSSH remote login client
    telnet (1)           - user interface to the TELNET protocol
    nc (1)               - arbitrary TCP and UDP connections and listens
    openssl (1ssl)       - OpenSSL command line program
    s_client (1ssl)      - OpenSSL application commands
    nmap (1)             - Network exploration tool and security / port scanner


<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

Okay we need to send the old password to a connection on our maschine on port `3000` to get the new password.

There are several possibilites to do that. 

I would recommend `nc/netcat` or classic `telnet`.

Lets check the manuals here:

```bash

TELNET(1)                                      User's Reference Manual                                      TELNET(1)

NAME
       telnet — user interface to the TELNET protocol

SYNOPSIS
       telnet [options ...] [host [port]]

DESCRIPTION
       The  telnet  command is used to communicate with another host using the TELNET protocol.  If telnet is invoked
       without the host argument, it enters command mode, indicated by its prompt (telnet>).  In this  mode,  it  ac‐
       cepts  and  executes  the commands listed below.  If it is invoked with arguments, it performs an open command
       with those arguments.

NC(1)                                           General Commands Manual                                         NC(1)

NAME
       nc — arbitrary TCP and UDP connections and listens

SYNOPSIS
       nc  [-46bCDdFhklNnrStUuvZz]  [-I  length]  [-i  interval] [-M ttl] [-m minttl] [-O length] [-P proxy_username]
          [-p source_port] [-q seconds] [-s  sourceaddr]  [-T  keyword]  [-V  rtable]  [-W  recvlimit]  [-w  timeout]
          [-X proxy_protocol] [-x proxy_address[:port]] [destination] [port]

DESCRIPTION
       The  nc  (or  netcat) utility is used for just about anything under the sun involving TCP, UDP, or Unix-domain
       sockets.  It can open TCP connections, send UDP packets, listen on arbitrary TCP and UDP ports, do port  scan‐
       ning,  and  deal  with  both IPv4 and IPv6.  Unlike telnet(1), nc scripts nicely, and separates error messages
       onto standard error instead of sending them to standard output, as telnet(1) does with some.

```


Okay both command would have the same syntax, `nc` has a lot more possiblites than `telnet`.

Lets try `telnet` first.

We need

    telnet  address  port


```bash

bandit14@bandit:~$ telnet localhost 30000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo

```

Okay that worked perfectly. 

Now lets try the same with `nc`.

```bash
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo

```

Good, works too.

I would recommend to use `nc`because telnet cant do encrypted connections.

Password: `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`


## Explanation: 

#### Using Telnet and Netcat for Network Communication

In network communication, tools like `telnet` and `netcat` (or `nc`) play crucial roles in connecting to remote services and sending data over TCP or UDP streams. Understanding these tools and their functionalities is essential for various networking tasks.

#### **Telnet**

**Telnet** is a simple network protocol used to establish a text-based communication session with a remote server. It operates over TCP and is often used for testing and debugging network services. Here's what you need to know:

- **Usage**: Telnet can connect to a specific port on a remote machine, allowing you to interact with services running on that port.
- **Command Syntax**: 
  ```bash
  telnet [host] [port]

#### Using Netcat for Network Communication

**Netcat** (or `nc`) is a versatile tool for network communication, often referred to as the "Swiss army knife" of networking. It supports both TCP and UDP protocols and offers additional features for various networking tasks. Understanding how to use `netcat` effectively is essential for network management and security assessments.

#### **Netcat (nc)**

**Netcat** is a powerful utility used for network communication that allows you to open connections, listen on ports, and send or receive data. Its versatility makes it suitable for a wide range of networking tasks.

- **Usage**: Netcat can be used to establish TCP/UDP connections, listen for incoming connections, and send or receive data. It is highly scriptable and useful for network diagnostics, security testing, and data transfer.
- **Command Syntax**: 
  ```bash
  nc [options] [host] [port]
