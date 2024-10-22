---
title: "[SOC-Level 1] Tryhackme - Snort Live Attacks"
date: 2024-10-04 14:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, Snort Live Attacks]
tags: [soc,tryhackme,blueteam,snort]
author: Christoph K
---
[TryHackMe- Snort Live Attacks ](https://tryhackme.com/r/room/snortchallenges2)



## Scenario 1: Brute-Force

    First of all, start Snort in sniffer mode and try to figure out the attack source, service and port.

    Then, write an IPS rule and run Snort in IPS mode to stop the brute-force attack. Once you stop the attack properly, you will have the flag on the desktop!

    Here are a few points to remember:

    Create the rule and test it with "-A console" mode. 
    Use "-A full" mode and the default log path to stop the attack.
    Write the correct rule and run the Snort in IPS "-A full" mode.
    Block the traffic at least for a minute and then the flag file will appear on your desktop.


Questions:

- Stop the attack and get the flag (which will appear on your Desktop)
- What is the name of the service under attack?
- What is the used protocol/port in the attack?






### Stop the attack and get the flag (which will appear on your Desktop)


First lets check the traffic to understand the attack better. We use `snort` in `sniffer mode`.


`sudo snort -v -l . `

We scan the log with:

`sudo snort -r SNORT.LOG.1234556 -X`

We can find a lot of `Port 22` connections. Lets try it different.


A Brute-Force Attack normally sends a flood of `TCP SYN packets`.

Lets filter our output a bit.


`snort -v eth0 'tcp[tcpflags] & tcp-syn != 0'`


Now we have to think a bit. We can see several connections from `10.10.245.36 ` to `10.10.140.29`sending `SYN packets` on port `22` which would be `SSH`. 




```bash

Initializing Output Plugins!
Snort BPF option: tcp[tcpflags] & tcp-syn != 0
pcap DAQ configured to passive.
Acquiring network traffic from "eth0".
Decoding Ethernet

        --== Initialization Complete ==--

   ,,_     -*> Snort! <*-
  o"  )~   Version 2.9.7.0 GRE (Build 149) 
   ''''    By Martin Roesch & The Snort Team: http://www.snort.org/contact#team
           Copyright (C) 2014 Cisco and/or its affiliates. All rights reserved.
           Copyright (C) 1998-2013 Sourcefire, Inc., et al.
           Using libpcap version 1.9.1 (with TPACKET_V3)
           Using PCRE version: 8.39 2016-06-14
           Using ZLIB version: 1.2.11

Commencing packet processing (pid=1875)
WARNING: No preprocessors configured for policy 0.
10/06-03:15:50.987248 10.10.245.36:46626 -> 10.10.140.29:22
TCP TTL:64 TOS:0x0 ID:60336 IpLen:20 DgmLen:60 DF
******S* Seq: 0xB51412CF  Ack: 0x0  Win: 0xF507  TcpLen: 40
TCP Options (5) => MSS: 8961 SackOK TS: 1884575822 0 NOP WS: 7 
=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+
```


We start small first, we should first try to block the IP address, because we dont know, if the attacker uses other ports as well.


Lets build a rule which blocks that traffic:

`reject tcp any any <> 10.10.245.36 any (msg:"Potential Brute-force attack";sid:10000001;rev:1;)`


And after 10-20 Seconds we received a notification, that the attack was stopped.

"THM{81b7fef657f8aaa6e4e200d616738254}"

Answer:`THM{81b7fef657f8aaa6e4e200d616738254}`


### - What is the name of the service under attack?



We talked about this before. I saw a lot of `TCP SYN Packets` with Port `22` which is `SSH`


Anwer: `SSH`



### - What is the used protocol/port in the attack?


Also we talked about this before:

Answer:`tcp/22`

## Scenario 2: Reverse Shell  


Questions:
- Stop the attack and get the flag (which will appear on your Desktop)
- What is the used protocol/port in the attack?
- Which tool is highly associated with this specific port number?

### Stop the attack and get the flag (which will appear on your Desktop)

We use again the sniffing mode and check the logs.

`sudo snort -v -l . `


And I found the following:


![Snort Port 4444](assets/img/tryhackme/snort-live-challenge/snort-01.png)



When you further investigate the log you will notice, that there a many connection with Port `4444` which is a default port on some `reverse shell scripts`.

When you do the logging with `-X` then you can see in the payload, that there is `root@` which is an indicator of a reverse shell. 


Lets stop this attack!


Rules this time:

`reject tcp any 4444 <> any any (msg:"Reverse Shell Attack stopped"; sid:1000001; rev:1;)`


And running 

`sudo snort -c /etc/snort/local.rules -A full `

We got our flag on the desktop.

THM{0ead8c494861079b1b74ec2380d2cd24}

Answer: `THM{0ead8c494861079b1b74ec2380d2cd24}`


###  What is the used protocol/port in the attack?


Our port was `4444` and the proctol was `TCP`.

Answer: `TCP/4444`

### Which tool is highly associated with this specific port number?


I had to research a bit, because there a lot of possibilities for `reverse shells`.

After a while I found the correct answer:


[exploit-notes.hdks.org](https://exploit-notes.hdks.org/exploit/shell/reverse-shell-with-metasploit/)



Answer: `Metasploit`









