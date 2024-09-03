---
title: "Bandit: Level 15 - Level 16"
date: 2024-08-30 14:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 15

**Username:** `bandit15`  
**Password:** `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit16.html

    The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL/TLS encryption.

    Helpful note: Getting “DONE”, “RENEGOTIATING” or “KEYUPDATE”? Read the “CONNECTED COMMANDS” section in the manpage.

    Commands you may need to solve this level
    ssh, telnet, nc, ncat, socat, openssl, s_client, nmap, netstat, ss

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit15` on port `2220`.

We have several commands available for this task:


    ssh (1)              - OpenSSH remote login client
    telnet (1)           - user interface to the TELNET protocol
    nc (1)               - arbitrary TCP and UDP connections and listens
    openssl (1ssl)       - OpenSSL command line program
    s_client (1ssl)      - OpenSSL application commands
    nmap (1)             - Network exploration tool and security / port scanner


<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

Now we have the task with the encrypted variant. The command `openssl` looks correct here. Lets check the manual on this one.


```bash

OPENSSL(1SSL)                                                                       OpenSSL                                                                      OPENSSL(1SSL)

NAME
       openssl - OpenSSL command line program

SYNOPSIS
       openssl command [ options ... ] [ parameters ... ]

       openssl no-XXX [ options ]


 s_client
           This  implements a generic SSL/TLS client which can establish a transparent connection to a remote server speaking SSL/TLS. It's intended for testing purposes only
           and provides only rudimentary interface functionality but internally uses mostly all functionality of the OpenSSL ssl library.

```

`openssl` is a very "big" command, there are many things to do with `openssl` later. 

Okay so we can use `openssl` in combination with `s_client`.

Lets check the helpfile from `s_client`

```bash
bandit15@bandit:~$ openssl s_client -help
Usage: s_client [options] [host:port]

General options:
 -help                      Display this summary
 -engine val                Use engine, possibly a hardware device
 -ssl_client_engine val     Specify engine to be used for client certificate operations
 -ssl_config val            Use specified section for SSL_CTX configuration
 -ct                        Request and parse SCTs (also enables OCSP stapling)
 -noct                      Do not request or parse SCTs (default)
 -ctlogfile infile          CT log list CONF file

Network options:
 -host val                  Use -connect instead
 -port +int                 Use -connect instead
 -connect val               TCP/IP where to connect; default: 4433)
```

It seems, we can build an excrypted connection via `-connect`


```bash

bandit15@bandit:~$ openssl s_client -connect localhost:30001
CONNECTED(00000003)

    Start Time: 1725351598
    Timeout   : 7200 (sec)
    Verify return code: 18 (self-signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```


Perfect, that worked! We have our password.


Password: `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`


## Explanation: 

#### Using OpenSSL's s_client for SSL/TLS Connections

**OpenSSL** is a powerful toolkit for working with SSL/TLS protocols, and `s_client` is a useful command within OpenSSL for testing and debugging SSL/TLS connections. Here’s a basic overview of what `s_client` does and how to use it.

##### **What is s_client?**

`openssl s_client` is a command-line tool that allows you to connect to a remote server using SSL/TLS and test the server’s certificate and connection. It’s primarily used to:

- **Verify SSL/TLS Certificates**: Check if a server’s SSL/TLS certificate is valid.
- **Debug Connections**: Inspect the details of the SSL/TLS handshake and connection.
- **View Certificates**: Display information about the server’s SSL/TLS certificate.

#### **How to Use s_client**

To use `s_client`, you need to specify the remote server and port you want to connect to. Here’s the basic syntax:

```bash
openssl s_client -connect [host]:[port]
```