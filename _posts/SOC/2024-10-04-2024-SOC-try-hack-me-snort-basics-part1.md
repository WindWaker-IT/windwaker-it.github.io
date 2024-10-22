---
title: "[SOC-Level 1] Tryhackme - Snort Challenge Part 1"
date: 2024-10-04 12:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, Snort Challenge Part 1]
tags: [soc,tryhackme,blueteam,snort]
author: Christoph K
---
[TryHackMe- Snort Challenge ](https://tryhackme.com/r/room/snortchallenges1)


## Task 1: Writing IDS Rules (HTTP)



1. What is the number of detected packets?
2. What is the destination address of packet 63?
3. What is the ACK number of packet 64?
4. What is the SEQ number of packet 62? 
5. What is the TTL of packet 65?
6. What is the source IP of packet 65?
7. What is the source port of packet 65?





### 1. What is the number of detected packets?

    Write a single rule to detect "all TCP port 80 traffic" packets in the given pcap file. 


Lets write a rule for that into our `local.rules`file.


```bash


# ----------------
# LOCAL RULES
# ----------------
# This file intentionally does not come with signatures.  Put your local
# additions here.

alert tcp any any <> any 80 (msg: "TCP Port 80 packets";sid: 100000001;rev:1;)

```

The command to use our `ruleset` and the `pcap`file goes:

`snort -c local.rules -A full -r mx-3.pcap`

- `-c`	Defining the configuration file
- `-A`  Alert mode: full 


And we get the stats from our IDS:

```bash

Breakdown by protocol (includes rebuilt packets):
        Eth:          460 (100.000%)
       VLAN:            0 (  0.000%)
        IP4:          444 ( 96.522%)
       Frag:            0 (  0.000%)
       ICMP:          272 ( 59.130%)
        UDP:            8 (  1.739%)
        TCP:          164 ( 35.652%)
        IP6:            0 (  0.000%)
    IP6 Ext:            0 (  0.000%)
   IP6 Opts:            0 (  0.000%)
      Frag6:            0 (  0.000%)
      ICMP6:            0 (  0.000%)
       UDP6:            0 (  0.000%)
       TCP6:            0 (  0.000%)
     Teredo:            0 (  0.000%)
    ICMP-IP:            0 (  0.000%)
    IP4/IP4:            0 (  0.000%)
    IP4/IP6:            0 (  0.000%)

```


As we can see,we analysed `164` TCP packets.


Answer: `164`

### 2. What is the destination address of packet 63?

For this question we have to create a `logfile`. We can do that with `-l`


`snort -c local.rules -r mx-3.pcap -l .`

- `-l` creates a logfile on the following path ( in this case `.` is our current directory)


After creating our logfile, we can search the first `63`packets with `-n`:

`sudo snort -r snort.log.1728105072 -n 63`


```bash
=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+

WARNING: No preprocessors configured for policy 0.
05/13-10:17:10.295515 145.254.160.237:3371 -> 216.239.59.99:80
TCP TTL:128 TOS:0x0 ID:3917 IpLen:20 DgmLen:761 DF
***AP*** Seq: 0x36C21E28  Ack: 0x2E6B5384  Win: 0x2238  TcpLen: 20
=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+

===============================================================================
Run time for packet processing was 0.1595 seconds
Snort processed 63 packets.
Snort ran for 0 days 0 hours 0 minutes 0 seconds
   Pkts/sec:           63
===============================================================================
Memory usage summary:
```

Answer: `216.239.59.99`


### 3. What is the ACK number of packet 64?


We need a small change here. We need to change our last command to `-n 64`.


`sudo snort -r snort.log.1728105072 -n 64`


```bash
WARNING: No preprocessors configured for policy 0.
05/13-10:17:10.295515 145.254.160.237:3371 -> 216.239.59.99:80
TCP TTL:128 TOS:0x0 ID:3917 IpLen:20 DgmLen:761 DF
***AP*** Seq: 0x36C21E28  Ack: 0x2E6B5384  Win: 0x2238  TcpLen: 20
=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+

```
Answer: `0x2E6B538`




### 4. What is the SEQ number of packet 62? 


Another small change: `-n 62`

`sudo snort -r snort.log.1728105072 -n 64`


=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+

WARNING: No preprocessors configured for policy 0.
05/13-10:17:10.295515 145.254.160.237:3371 -> 216.239.59.99:80
TCP TTL:128 TOS:0x0 ID:3917 IpLen:20 DgmLen:761 DF
***AP*** Seq: 0x36C21E28  Ack: 0x2E6B5384  Win: 0x2238  TcpLen: 20
=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+

Answee: `0x36C21E28`

### 5. What is the TTL of packet 65?

Another small change: `-n 65`

Search for TTL:


```bash

+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+

WARNING: No preprocessors configured for policy 0.
05/13-10:17:10.325558 145.254.160.237:3372 -> 65.208.228.223:80
TCP TTL:128 TOS:0x0 ID:3918 IpLen:20 DgmLen:40 DF
***A**** Seq: 0x38AFFFF3  Ack: 0x114C81E4  Win: 0x25BC  TcpLen: 20
=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+
```

Answer: `128`


### 6. What is the source IP of packet 65?

We can use the data from our last question. 


```bash

+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+

WARNING: No preprocessors configured for policy 0.
05/13-10:17:10.325558 145.254.160.237:3372 -> 65.208.228.223:80
TCP TTL:128 TOS:0x0 ID:3918 IpLen:20 DgmLen:40 DF
***A**** Seq: 0x38AFFFF3  Ack: 0x114C81E4  Win: 0x25BC  TcpLen: 20
=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+
```

Answer: `145.254.160.237`


### 7. What is the source port of packet 65?


```bash

+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+

WARNING: No preprocessors configured for policy 0.
05/13-10:17:10.325558 145.254.160.237:3372 -> 65.208.228.223:80
TCP TTL:128 TOS:0x0 ID:3918 IpLen:20 DgmLen:40 DF
***A**** Seq: 0x38AFFFF3  Ack: 0x114C81E4  Win: 0x25BC  TcpLen: 20
=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+
```

Answer: `3372`




## Task 2: Writing IDS Rules (FTP)




1. What is the number of detected packets?
2. What is the FTP service name?
3. Write a rule to detect failed FTP login attempts in the given pcap.
4. What is the number of detected packets?
5. What is the number of detected packets?
6. What is the number of detected packets?



### 1. What is the number of detected packets?


    Write a single rule to detect "all TCP port 21"  traffic in the given pcap.



We write again our rule, this time for `port 21`

`alert tcp any any <> any 21 (msg:"FTP Alert";sid:10000002;rev:1;)`


and we use `snort` to use our created rule for the `pcap`file.

`sudo snort -c local.rules -A full -r ftp-png-gif.pcap`

```
Action Stats:
     Alerts:          307 ( 72.922%)
     Logged:          307 ( 72.922%)
     Passed:            0 (  0.000%)
Limits:
```

Answer: `307`

### 2. What is the FTP service name?


And we have to create a `logfile` again with `-l`

`sudo snort -c local.rules -r ftp-png-gif.pcap -l .`

After that we can try to read the `logfile`with `strings`

`sudo strings snort.log.1728106345 `

For filtering the log we can check the `FTP Server Returns`

[FTP Server Returns](https://hstechdocs.helpsystems.com/manuals/globalscape/cuteftpmacpro3/Numbered_FTP_status_and_error_codes.htm)


We can use `220` `Service Ready for new user`

```bash
ubuntu@ip-10-10-116-7:~/Desktop/Exercise-Files/TASK-3 (FTP)$ sudo strings snort.log.1728106345 |grep 220
}220 Microsoft FTP Service
~220 Microsoft FTP Service
220 Microsoft FTP Service
220 Microsoft FTP Service

```

Answer: `Microsoft FTP Service`


### 3. What is the number of detected packets?


    Write a rule to detect failed FTP login attempts in the given pcap.


When we check again our `Server Return Table` we can use `530` for bad login attempts. 

I created following rule:



`alert tcp any any <> any 21 (msg:"FTP failed login -530 response";content:"530 "; depth:4; sid:10000001;rev:1;)`


- `content`: Matches the content "530 " in the payload, which is the FTP response code for failed login attempts
- `depth:4`: Limits the search to the first 4 bytes of the payload (ensures it checks only at the start of the server's response).

```bash
===============================================================================
Action Stats:
     Alerts:           41 (  9.739%)
     Logged:           41 (  9.739%)
     Passed:            0 (  0.000%)
Limits:
      Match:            0
      Queue:            0
        Log:            0
```



Answer: `41`

### 4. What is the number of detected packets?


    Write a rule to detect successful FTP logins in the given pcap.


We use `230`as FTP Server Return and change the rule:

`alert tcp any any <> any 21 (msg:"FTP successful login -230 response";content:"230 "; depth:4; sid:10000001;rev:1;)`

```bash
===============================================================================
Action Stats:
     Alerts:            1 (  0.238%)
     Logged:            1 (  0.238%)
     Passed:            0 (  0.000%)
Limits:
```

Answer: `1`



### 5. What is the number of detected packets?


    Write a rule to detect FTP login attempts with a valid username but no password entered yet.


We change our rule to `content:331`. `331` means that there was no password entered/ waiting for password.

`alert tcp any any <> any 21 (msg:"FTP no password -331 response";content:"331 "; depth:4; sid:10000001;rev:1;)`

```bash
===============================================================================
Action Stats:
     Alerts:           42 (  9.976%)
     Logged:           42 (  9.976%)
     Passed:            0 (  0.000%)

```

Anser: `42`

### 6. What is the number of detected packets?


    Write a rule to detect FTP login attempts with the "Administrator" username but no password entered yet.


We need to adjust our `content` in our rule:


`alert tcp any any <> any 21 (msg:"FTP no password Administrator -331 response";content:"331 "; content:"Administrator"; sid:10000001;rev:1;)`


```bash
===============================================================================
Action Stats:
     Alerts:            7 (  1.663%)
     Logged:            7 (  1.663%)
     Passed:            0 (  0.000%)
```

Answer: `7`


Check out `Snort Challenge Part 2`

