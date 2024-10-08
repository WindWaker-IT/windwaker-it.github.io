---
title: "[SOC-Level 1] Tryhackme - Zeek"
date: 2024-10-06 12:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, Zeek]
tags: [soc,tryhackme,blueteam,zeek]
author: Christoph K
---
[TryHackMe- ZEEK ](https://tryhackme.com/r/room/zeekbro)



## Introduction

#### What is Zeek?

Zeek (formerly Bro) is an open-source and commercial passive Network Monitoring tool (traffic analysis framework) developed by Lawrence Berkeley Labs.
Zeek differs from known monitoring and IDS/IPS tools by providing a wide range of detailed logs ready to investigate both for forensics and data analysis actions. Currently, Zeek provides 50+ logs in 7 categories.

#### How does Zeek detect abnormal activities?

It does this by analyzing data, like who is talking to whom, what protocols are being used, and if there are any potential security threats. Zeek is not just an Intrusion Detection System (IDS); it also logs detailed information about network activities for further investigation. 

#### Who uses Zeek?
It’s used by security teams to understand and monitor network behavior, detect attacks, and analyze incidents.



### Difference to Snort

| Tool   | ZEEK    | SNORT    |
|-------------|-------------|-------------|
| Capabilities | NSM and IDS framework. It is heavily focused on network analysis. It is more focused on specific threats to trigger alerts. The detection mechanism is focused on events. | An IDS/IPS system. It is heavily focused on signatures to detect vulnerabilities. The detection mechanism is focused on signature patterns and packets. |
| CONS | Hard to use; The analysis is done out of the Zeek, manually or by automation | Hard to detect complex threats. |
| PROS |It provides in-depth traffic visibility; Useful for threat hunting; Ability to detect complex threats; It has a scripting language and supports event correlation; Easy to read logs. | Easy to write rules; Cisco supported rules; Community support |
| Common Use Case | Network monitoring; In-depth traffic investigation; Intrusion detecting in chained events.  | Intrusion detection and prevention.; Stop known attacks/threats.|





## Task 1
- What is the installed Zeek instance version number?
- What is the version of the ZeekControl module?
- Investigate the "sample.pcap" file. What is the number of generated alert files?



### What is the installed Zeek instance version number?

We can check the version number with `-v`

```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-2# zeek -v
zeek version 4.2.1
```

Answer: `4.2.1`

###  What is the version of the ZeekControl module?

With `zeekctl` we can go into the service mode and check the version.

``` bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-2# zeekctl
Warning: new zeek version detected (run the zeekctl "deploy" command)

Welcome to ZeekControl 2.4.0

```

Answer: `2.4.0`
 
###  Investigate the "sample.pcap" file. What is the number of generated alert files?

We open the `sample.pcap`file with flag `-r` and use `-C` for ignoring checksum errors.

```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-2# zeek -C -r sample.pcap 
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-2# ls -l
total 444
-rwxr-xr-x 1 ubuntu ubuntu     46 Apr  3  2022 clear-logs.sh
-rw-r--r-- 1 root   root    11375 Oct  8 02:12 conn.log
-rw-r--r-- 1 root   root      761 Oct  8 02:12 dhcp.log
-rw-r--r-- 1 root   root     2914 Oct  8 02:12 dns.log
-rw-r--r-- 1 root   root     2532 Oct  8 02:12 ntp.log
-rw-r--r-- 1 root   root      254 Oct  8 02:12 packet_filter.log
-rw-r--r-- 1 ubuntu ubuntu 407510 Mar  3  2017 sample.pcap
-rw-r--r-- 1 root   root      529 Oct  8 02:12 snmp.log
-rw-r--r-- 1 root   root      703 Oct  8 02:12 ssh.log
-rw-r--r-- 1 root   root     1561 Oct  8 02:12 syslog.log
```

## Task 2


- Investigate the sample.pcap file. Investigate the dhcp.log file. What is the available hostname?
- Investigate the dns.log file. What is the number of unique DNS queries?
- Investigate the conn.log file. What is the longest connection duration?


### Investigate the sample.pcap file. Investigate the dhcp.log file. What is the available hostname?

In this section we learned how to work with the log files. We can use `zeek-cut` for getting better visible information.

```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-3# cat dhcp.log |zeek-cut host_name
Microknoppix

```

Answer: `Microknoppix`


###  Investigate the dns.log file. What is the number of unique DNS queries?

Here we can `zeek-cut` for `query`:


```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-3# cat dns.log |zeek-cut query
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
blog.webernetz.net
ip.webernetz.net
```

We have 2 unique queries:
blog.webernetz.net
ip.webernetz.net

Answer: `2`

### Investigate the conn.log file. What is the longest connection duration?

To find out the duration we cut the log with `duration` and for better visibility we can also use `sort -nr` (`-n`Numbers ; `-r` reverse (descending))


```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-3# cat conn.log |zeek-cut duration |sort -nr
332.319364
331.791038
329.899861
```


Answer: ` 332.319364`

## Task 3

- Investigate the http.pcap file. Create the  HTTP signature shown in the task and investigate the pcap. What is the source IP of the first event?
- What is the source port of the second event?
- What is the total number of the sent and received packets from source port 38706?
- Investigate the notice.log. What is the number of unique events?
- What is the number of ftp-brute signature matches?


### Investigate the http.pcap file. Create the  HTTP signature shown in the task and investigate the pcap. What is the source IP of the first event?


First we create our signature to find "passwords" in the payload from http traffic.

Rule:
```bash
signature http-password {
    ip-proto == tcp
    dst-port == 80
    payload /.*password.*/
    event "Password Cleartext found."
}
```

After that we apply the signature into our `pcap` file via `-s`.

```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-5/http# zeek -C -r http.pcap -s http-password.sig 
```

Now we filter our results. We need the `src_addr` and the `event_msg`
```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-5/http# cat signatures.log |zeek-cut  src_addr event_msg
10.10.57.178	10.10.57.178: Password Cleartext found.
```

Answer:`10.10.57.178`

### What is the source port of the second event?

We filter our log a little bit more. This time we add `src_port` and add some line numbers to our output with `nc`

```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-5/http# cat signatures.log  |zeek-cut  src_addr event_msg src_port |nl 
     1	10.10.57.178	10.10.57.178: Password Cleartext found.	38706
     2	10.10.57.178	10.10.57.178: Password Cleartext found.	38712
```

Answer: `38712`

### What is the total number of the sent and received packets from source port 38706?

    Investigate the conn.log.

Here we filter for `received` and `sent` packets as well as for `origin port`.

```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-5/http# cat conn.log |zeek-cut id.orig_p orig_pkts resp_pkts
38704	4	2
38706	11	9
38708	4	2
38710	4	2
38712	6	5

```


### Investigate the notice.log. What is the number of unique events?


    Create the global rule shown in the task and investigate the ftp.pcap file.


Okay we create 2 signatures now. The first signatures checks if there is a `Username Input` and the second one sets the event `Brute force attempt` when we get a server return `530`.

```bash

signature ftp-username {
    ip-proto == tcp
    ftp /.*USER.*/
    event "FTP Username Input Found!"
}

signature ftp-brute {
    ip-proto == tcp
    payload /.*530*.*Login.*incorrect.*/
    event "FTP Brute-force Attempt!"
}

```

And we have to filter our Output to `unique events`.

We do that with `uid`; `sort`; `uniq` and `nl`

`cat notice.log |zeek-cut uid | sort |uniq |nl `

And we get as result: 1413

Answer: `1413`



###  What is the number of ftp-brute signature matches?

We wrote in our signature message `FTP Brute-force Attempt` which we can filter now. 
We use `grep` and filter for the word and we use `nl` to "count"..


```  bash
  1393  10.121.70.151: FTP Brute-force Attempt!
  1394	10.121.70.151: FTP Brute-force Attempt!
  1395	10.121.70.151: FTP Brute-force Attempt!
  1396	10.121.70.151: FTP Brute-force Attempt!
  1397	10.121.70.151: FTP Brute-force Attempt!
  1398	10.121.70.151: FTP Brute-force Attempt!
  1399	10.121.70.151: FTP Brute-force Attempt!
  1400	10.121.70.151: FTP Brute-force Attempt!
  1401	10.121.70.151: FTP Brute-force Attempt!
  1402	10.121.70.151: FTP Brute-force Attempt!
  1403	10.121.70.151: FTP Brute-force Attempt!
  1404	10.121.70.151: FTP Brute-force Attempt!
  1405	10.121.70.151: FTP Brute-force Attempt!
  1406	10.121.70.151: FTP Brute-force Attempt!
  1407	10.121.70.151: FTP Brute-force Attempt!
  1408	10.121.70.151: FTP Brute-force Attempt!
  1409	10.121.70.151: FTP Brute-force Attempt!
  1410	10.121.70.151: FTP Brute-force Attempt!
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-5/ftp# cat  notice.log |zeek-cut msg |grep Brute* |nl 
```

Answer: `1410`

## Task 4 

- Investigate the smallFlows.pcap file. Investigate the dhcp.log file. What is the domain value of the "vinlap01" host?
- Investigate the bigFlows.pcap file. Investigate the dhcp.log file. What is the number of identified unique hostnames?
- Investigate the dhcp.log file. What is the identified domain value?



### Investigate the smallFlows.pcap file. Investigate the dhcp.log file. What is the domain value of the "vinlap01" host?

We filter out log with `domain` and `host name`

```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-6/smallflow# cat dhcp.log |zeek-cut domain host_name
-	student01-PC
astaro_vineyard	vinlap01
```

Answer: `astaro_vineyard`




### Investigate the bigFlows.pcap file. Investigate the dhcp.log file. What is the number of identified unique hostnames?

Now we can use the `zeek` script, which displays all `hostnames`. We can also dump the erros into `dev/null` and we use `sort`; `uniq` and `nl` to get our wished result.


```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-6/bigflow# zeek -C -r bigFlows.pcap dhcp-hostname.zeek 2>/dev/null  | sort|uniq|nl
     1	JDT081
     2	JDT094
     3	JDT096
     4	JDT100
     5	JDT107
     6	JDT115
     7	JDT120
     8	JDT123
     9	JDT131
    10	JDT134
    11	JDT153
    12	JDT168
    13	JDT80
    14	JDT91
    15	JDT95
    16	JLT108
    17	m30-sqdesk

```

Answer: `17`


### Investigate the dhcp.log file. What is the identified domain value?

We filter again for domain:

```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-6/bigflow# cat dhcp.log |zeek-cut host_name domain
JDT115	jaalam.net
m30-sqdesk	-
``` 

Answer: `jaalam.net`

## Task 5

- Investigate the sample.pcap file with 103.zeek script. Investigate the terminal output. What is the number of the detected new connections?
- Investigate the ftp.pcap file with ftp-admin.sig signature and  201.zeek script. Investigate the signatures.log file. What is the number of signature hits?
- Investigate the signatures.log file. What is the total number of "administrator" username detections?
- Investigate the ftp.pcap file with all local scripts, and investigate the loaded_scripts.log file. What is the total number of loaded scripts?
- Investigate the ftp-brute.pcap file with "/opt/zeek/share/zeek/policy/protocols/ftp/detect-bruteforcing.zeek" script. Investigate the notice.log file. What is the total number of brute-force detections?


### - Investigate the sample.pcap file with 103.zeek script. Investigate the terminal output. What is the number of the detected new connections?


When we run the script, we get as output several lines `New Connection Found!` and some details to the connection.

So we filter the output a bit to get our answer: 

```bash

    81	New Connection Found!
    82	New Connection Found!
    83	New Connection Found!
    84	New Connection Found!
    85	New Connection Found!
    86	New Connection Found!
    87	New Connection Found!
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-7/101# zeek -C -r sample.pcap 103.zeek  |grep "Connection" |nl
```

Answer: `87`


### Investigate the ftp.pcap file with ftp-admin.sig signature and  201.zeek script. Investigate the signatures.log file. What is the number of signature hits?


For this answer we have several options. We can count again the hits in the output

```bash

  1401	Signature hit! --> #FTP-Admin 
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-7/201# zeek -C -r ftp.pcap 201.zeek -s ftp-admin.sig |nl

```


`or`


We filter the `log`.

```bash

  1399	10.234.125.254: FTP Username Input Found!
  1400	10.234.125.254: FTP Username Input Found!
  1401	10.234.125.254: FTP Username Input Found!
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-7/201# cat signatures.log |zeek-cut event_msg |nl


```

Answer: `1404`


### Investigate the signatures.log file. What is the total number of "administrator" username detections?

We can just search for `administrato` with `grep` and count the `lines`.

```bash 
Found!	USER administrator	-	-
   731	1024380775.605088	CrmOwd1l9JR2wVzQl3	10.234.125.254	2923	10.121.70.151	21	Signatures::Sensitive_Signature	ftp-admin	10.234.125.254: FTP Username Input Found!	USER administrator	-	-
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-7/201# cat signatures.log |grep administrator |nl
```

Answer: `731`


### Investigate the ftp.pcap file with all local scripts, and investigate the loaded_scripts.log file. What is the total number of loaded scripts?

We alreay know, that local scripts are in `/opt` so we can filter the output of the log with `grep` and `nl`.



```bash

   496	    /opt/zeek/share/zeek/policy/frameworks/intel/seen/smtp.zeek
   497	    /opt/zeek/share/zeek/policy/frameworks/intel/seen/smtp-url-extraction.zeek
   498	    /opt/zeek/share/zeek/policy/frameworks/intel/seen/x509.zeek
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-7/201# cat loaded_scripts.log |grep /opt/* |nl
``` 


###  Investigate the ftp-brute.pcap file with "/opt/zeek/share/zeek/policy/protocols/ftp/detect-bruteforcing.zeek" script. Investigate the notice.log file. What is the total number of brute-force detections?

We use the mentioned script:
`zeek -C -r ftp-brute.pcap /opt/zeek/share/zeek/policy/protocols/ftp/detect-bruteforcing.zeek
`

and then we filter our outcome for `note`. 

```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-7/202# cat notice.log |zeek-cut note
FTP::Bruteforcing
FTP::Bruteforcing
```

Answer: `2`

## Task 6

- Investigate the case1.pcap file with intelligence-demo.zeek script. Investigate the intel.log file. Look at the second finding, where was the intel info found? 
- Investigate the http.log file. What is the name of the downloaded .exe file?
- Investigate the case1.pcap file with hash-demo.zeek script. Investigate the files.log file. What is the MD5 hash of the downloaded .exe file?
- Investigate the case1.pcap file with file-extract-demo.zeek script. Investigate the "extract_files" folder. Review the contents of the text file. What is written in the file?


### Investigate the case1.pcap file with intelligence-demo.zeek script. Investigate the intel.log file. Look at the second finding, where was the intel info found? 

Lets filter again our `log`. We need to find out the location of it so lets check for `seen.where`.

```bash


root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-8# cat intel.log |zeek-cut matched seen.indicator seen.where|nl
     1	Intel::DOMAIN	smart-fax.com	DNS::IN_REQUEST
     2	Intel::DOMAIN	smart-fax.com	HTTP::IN_HOST_HEADER


```

Answer: `IN_HOST_HEADER`

### Investigate the http.log file. What is the name of the downloaded .exe file?


We investigate the `http.log`. We have to filter for `uri`to get the path and filename.

```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-8# cat http.log |zeek-cut uid method host trans_depth uri
CQL2oFWwPuJFVT5Z5	GET	www.msftncsi.com	1	/ncsi.txt
CtHEh74H3yiRNz8Dm3	GET	smart-fax.com	1	/Documents/Invoice&MSO-Request.doc
CRBeMB4R8dqNqt3IC1	GET	smart-fax.com	1	/knr.exe
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-8# 

```

Answer: `knr.exe`


### Investigate the case1.pcap file with hash-demo.zeek script. Investigate the files.log file. What is the MD5 hash of the downloaded .exe file?


We filter our `files.log`now for `mime_type` and `md5`. As we know from last tast, the downloaded file is an `.exe`. (`x-dosecex`)

```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-8# cat files.log |zeek-cut filename mime_type md5
-	text/plain	cd5a4d3fdd5bffc16bf959ef75cf37bc
-	application/msword	b5243ec1df7d1d5304189e7db2744128
-	application/x-dosexec	cc28e40b46237ab6d5282199ef78c464
```

Answer: `cc28e40b46237ab6d5282199ef78c464`

### Investigate the case1.pcap file with file-extract-demo.zeek script. Investigate the "extract_files" folder. Review the contents of the text file. What is written in the file?


Lets see what kind of files we have here:

```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-8/extract_files# ls -l
total 2700
-rw-r--r-- 1 root root      14 Oct  8 05:21 extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja
-rw-r--r-- 1 root root  323072 Oct  8 05:21 extract-1561667889.703239-HTTP-FB5o2Hcauv7vpQ8y3
-rw-r--r-- 1 root root 2437120 Oct  8 05:21 extract-1561667899.060086-HTTP-FOghls3WpIjKpvXaEl
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-8/extract_files# file *
extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja:  ASCII text, with no line terminators
extract-1561667889.703239-HTTP-FB5o2Hcauv7vpQ8y3:  Composite Document File V2 Document, Little Endian, Os: Windows, Version 6.3, Code page: 1252, Template: Normal.dotm, Last Saved By: Administrator, Revision Number: 2, Name of Creating Application: Microsoft Office Word, Create Time/Date: Thu Jun 27 18:24:00 2019, Last Saved Time/Date: Thu Jun 27 18:24:00 2019, Number of Pages: 1, Number of Words: 0, Number of Characters: 1, Security: 0
extract-1561667899.060086-HTTP-FOghls3WpIjKpvXaEl: PE32 executable (GUI) Intel 80386, for MS Windows
```

Okay the file `extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja` is the `ASCII text file`. Lets open it:

```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-8/extract_files# cat extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja 
Microsoft NCSIroot@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-8/extract_files# 

```

And we can see the Answer in front of our command line:

Answer: `Microsoft NCSI`

## Task 7

- Investigate the http.pcap file with the zeek-sniffpass module. Investigate the notice.log file. Which username has more module hits?
- Investigate the case2.pcap file with geoip-conn module. Investigate the conn.log file. What is the name of the identified City?
- Which IP address is associated with the identified City?
- Investigate the case2.pcap file with sumstats-counttable.zeek script. How many types of status codes are there in the given traffic capture?


### Investigate the http.pcap file with the zeek-sniffpass module. Investigate the notice.log file. Which username has more module hits?

We run `zeek` on our `pcap` file with `zeek-sniffpass`:

`zeek -C -r http.pcap /opt/zeek/share/zeek/site/zeek-sniffpass`


And filter our output:
```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-9/cleartext-pass# cat notice.log|zeek-cut uid msg 
CIo6ux3Pq6fduLj6U1	Password found for user BroZeek
CIo6ux3Pq6fduLj6U1	Password found for user BroZeek
CIo6ux3Pq6fduLj6U1	Password found for user BroZeek
CJJzyL14bTQmutCJz1	Password found for user ZeekBro
CJJzyL14bTQmutCJz1	Password found for user ZeekBro
```

Brozeek has 3 hits, ZeekBro has 2 hits.

Answer: `BroZeek`


### Investigate the case2.pcap file with geoip-conn module. Investigate the conn.log file. What is the name of the identified City?

Lets use the `geoip-conn`module.

`zeek -Cr case2.pcap geoip-conn`


```bash
root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-9/geoip-conn# cat conn.log |zeek-cut id.resp_h geo.resp.city |sort |uniq
10.6.29.1	-
116.203.71.114	-
173.0.82.206	-
173.0.82.211	-
23.2.46.147	-
23.77.86.54	Chicago
```

Answer: `Chicago`

### Which IP address is associated with the identified City?


In the question before answered

Answer: `23.77.86.54`

### Investigate the case2.pcap file with sumstats-counttable.zeek script. How many types of status codes are there in the given traffic capture?

We use the `sumstats-counttable.zeek` module now and filter the output with `sort`and `uniq`

```bash

root@ip-10-10-225-16:/home/ubuntu/Desktop/Exercise-Files/TASK-9/geoip-conn# zeek -Cr case2.pcap sumstats-counttable.zeek |sort|uniq
Host: 116.203.71.114
Host: 23.77.86.54
status code: 200, count: 26
status code: 301, count: 4
status code: 302, count: 4
status code: 404, count: 6

```

As we can see, we have `4` different status codes.



Answer: `4`




## Thoughts:

Very useful tool in CLI form but you really have to know how to filter `logs` and `output`. 
I used a lot of times `head` for checking the `field_types`. 

What did we learn`
- How to search `logs`
- Use CLI commands to `filter` the logs
- Zeek Signatures, Scripts and Frameworks
- Zeek Packages


Thank you :-) 
