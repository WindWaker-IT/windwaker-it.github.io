---
title: "Bandit: Level 13 - Level 14"
date: 2024-08-30 13:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 13

**Username:** `bandit13`  
**Password:** `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit14.html

    The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on

    Commands you may need to solve this level
    ssh, telnet, nc, openssl, s_client, nmap

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit13` on port `2220`.

We have several commands available for this task:


    ssh (1)              - OpenSSH remote login client
    telnet (1)           - user interface to the TELNET protocol
    nc (1)               - arbitrary TCP and UDP connections and listens
    openssl (1ssl)       - OpenSSL command line program
    s_client (1ssl)      - OpenSSL application commands
    nmap (1)             - Network exploration tool and security / port scanner


<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

Okay we are working now with connections. Lets check what we have here.


```bash
bandit13@bandit:~$ ls
sshkey.private
bandit13@bandit:~$ cat sshkey.private
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAxkkOE83W2cOT7IWhFc9aPaaQmQDdgzuXCv+ppZHa++buSkN+
gg0tcr7Fw8NLGa5+Uzec2rEg0WmeevB13AIoYp0MZyETq46t+jk9puNwZwIt9XgB
ZufGtZEwWbFWw/vVLNwOXBe4UWStGRWzgPpEeSv5Tb1VjLZIBdGphTIK22Amz6Zb
ThMsiMnyJafEwJ/T8PQO3myS91vUHEuoOMAzoUID4kN0MEZ3+XahyK0HJVq68KsV
ObefXG1vvA3GAJ29kxJaqvRfgYnqZryWN7w3CHjNU4c/2Jkp+n8L0SnxaNA+WYA7
jiPyTF0is8uzMlYQ4l1Lzh/8/MpvhCQF8r22dwIDAQABAoIBAQC6dWBjhyEOzjeA
J3j/RWmap9M5zfJ/wb2bfidNpwbB8rsJ4sZIDZQ7XuIh4LfygoAQSS+bBw3RXvzE
pvJt3SmU8hIDuLsCjL1VnBY5pY7Bju8g8aR/3FyjyNAqx/TLfzlLYfOu7i9Jet67
xAh0tONG/u8FB5I3LAI2Vp6OviwvdWeC4nOxCthldpuPKNLA8rmMMVRTKQ+7T2VS
nXmwYckKUcUgzoVSpiNZaS0zUDypdpy2+tRH3MQa5kqN1YKjvF8RC47woOYCktsD
o3FFpGNFec9Taa3Msy+DfQQhHKZFKIL3bJDONtmrVvtYK40/yeU4aZ/HA2DQzwhe
ol1AfiEhAoGBAOnVjosBkm7sblK+n4IEwPxs8sOmhPnTDUy5WGrpSCrXOmsVIBUf
laL3ZGLx3xCIwtCnEucB9DvN2HZkupc/h6hTKUYLqXuyLD8njTrbRhLgbC9QrKrS
M1F2fSTxVqPtZDlDMwjNR04xHA/fKh8bXXyTMqOHNJTHHNhbh3McdURjAoGBANkU
1hqfnw7+aXncJ9bjysr1ZWbqOE5Nd8AFgfwaKuGTTVX2NsUQnCMWdOp+wFak40JH
PKWkJNdBG+ex0H9JNQsTK3X5PBMAS8AfX0GrKeuwKWA6erytVTqjOfLYcdp5+z9s
8DtVCxDuVsM+i4X8UqIGOlvGbtKEVokHPFXP1q/dAoGAcHg5YX7WEehCgCYTzpO+
xysX8ScM2qS6xuZ3MqUWAxUWkh7NGZvhe0sGy9iOdANzwKw7mUUFViaCMR/t54W1
GC83sOs3D7n5Mj8x3NdO8xFit7dT9a245TvaoYQ7KgmqpSg/ScKCw4c3eiLava+J
3btnJeSIU+8ZXq9XjPRpKwUCgYA7z6LiOQKxNeXH3qHXcnHok855maUj5fJNpPbY
iDkyZ8ySF8GlcFsky8Yw6fWCqfG3zDrohJ5l9JmEsBh7SadkwsZhvecQcS9t4vby
9/8X4jS0P8ibfcKS4nBP+dT81kkkg5Z5MohXBORA7VWx+ACohcDEkprsQ+w32xeD
qT1EvQKBgQDKm8ws2ByvSUVs9GjTilCajFqLJ0eVYzRPaY6f++Gv/UVfAPV4c+S0
kAWpXbv5tbkkzbS0eaLPTKgLzavXtQoTtKwrjpolHKIHUz6Wu+n4abfAIRFubOdN
/+aLoRQ0yBDRbdXMsZN/jvY44eM+xRLdRVyMmdPtP8belRi2E2aEzA==
-----END RSA PRIVATE KEY-----

```

We have a file with a private key here, which we have to use for connecting with another user. We use for that `SSH`. Lets check the manual for private key files as login.

```bash
SSH(1)                                                                      General Commands Manual                                                                     SSH(1)

NAME
       ssh — OpenSSH remote login client

SYNOPSIS
       ssh  [-46AaCfGgKkMNnqsTtVvXxYy]  [-B  bind_interface]  [-b  bind_address]  [-c  cipher_spec]  [-D  [bind_address:]port]  [-E log_file] [-e escape_char] [-F configfile]
           [-I pkcs11] [-i identity_file] [-J destination] [-L address] [-l login_name] [-m mac_spec] [-O ctl_cmd] [-o option] [-P tag] [-p port] [-R address]  [-S  ctl_path]
           [-W host:port] [-w local_tun[:remote_tun]] destination [command [argument ...]]
       ssh [-Q query_option]
 -i identity_file
               Selects  a  file  from which the identity (private key) for public key authentication is read.  You can also specify a public key file to use the corresponding
               private key  that  is  loaded  in  ssh-agent(1)  when  the  private  key  file  is  not  present  locally.   The  default  is  ~/.ssh/id_rsa,  ~/.ssh/id_ecdsa,
               ~/.ssh/id_ecdsa_sk,  ~/.ssh/id_ed25519,  ~/.ssh/id_ed25519_sk and ~/.ssh/id_dsa.  Identity files may also be specified on a per-host basis in the configuration
               file.  It is possible to have multiple -i options (and multiple identities specified in configuration files).  If no certificates have been  explicitly  speci‐
               fied  by  the  CertificateFile directive, ssh will also try to load certificate information from the filename obtained by appending -cert.pub to identity file‐
               names.


```

So far so good. It seems we have just to use `-i`. Lets try it:


```bash
bandit13@bandit:~$ ssh bandit14@localhost -i ./sshkey.private  -p 2220
```

Good, we are connected :D 

Lets see if we have access to the password for this level:

```bash
bandit14@bandit:~$ cd /etc/bandit_pass
bandit14@bandit:/etc/bandit_pass$ cat bandit14
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS

```

Perfect, we can connect now direct and without private key. 

Password: `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`



## Explanation: 

#### Understanding SSH and Private Keys

SSH (Secure Shell) is a protocol used for securely accessing remote systems over a network. It provides a secure channel over an unsecured network by using encryption. The ssh command is the client application used to connect to remote servers via SSH.

When connecting to a server, you can use a `private key` for authentication instead of a password. This method is often more secure and convenient. The private key is usually paired with a public key that is stored on the server.

#### What is localhost?
localhost is a hostname that refers to the local machine you are working on. It is a loopback network interface, meaning that it points back to the same computer, allowing you to access services and resources running on your own machine. When you use localhost in an SSH command, you're connecting to the SSH server running on the same computer where you are executing the command.

#### What are Private Keys?

A private key is a cryptographic key used in public key cryptography. It is part of a key pair that includes a public key. The private key is kept secret and is used to decrypt data that has been encrypted with the corresponding public key.

In SSH, private keys are used to authenticate the user securely. When a server receives a connection request, it uses the public key to verify the authenticity of the private key provided by the client. If the private key matches the public key stored on the server, the client is granted access.


#### Using a Private Key with SSH
To connect to a server using a private key, you need to specify the private key file with the -i option. Here's the general syntax for using SSH with a private key:


    ssh -i [path-to-private-key] [username]@[hostname] -p [port]

In the provided example, the private key file sshkey.private was used to connect to the server bandit.labs.overthewire.org as user bandit14 on port 2220. Here’s how it was done:

    ssh bandit14@localhost -i ./sshkey.private -p 2220

