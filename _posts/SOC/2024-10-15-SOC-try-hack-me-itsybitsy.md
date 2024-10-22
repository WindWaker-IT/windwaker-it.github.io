---
title: "[SOC-Level 1] Tryhackme - ItsyBitsy - Investigate with ELK"
date: 2024-10-15 12:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, ItsyBitsy]
tags: [soc,tryhackme,blueteam,elk,kibana,logstash]
author: Christoph K
---
[TryHackMe- ItsyBitsy ](https://tryhackme.com/r/room/itsybitsy)


## Introduction & Scenario

    In this challenge room, we will take a simple challenge to investigate an alert by IDS regarding a potential C2 communication.

    During normal SOC monitoring, Analyst John observed an alert on an IDS solution indicating a potential C2 communication from a user Browne from the HR department. A suspicious file was accessed containing a malicious pattern THM:{ ________ }. A week-long HTTP connection logs have been pulled to investigate. Due to limited resources, only the connection logs could be pulled out and are ingested into the connection_logs index in Kibana.

## Tasks:

- How many events were returned for the month of March 2022?
- What is the IP associated with the suspected user in the logs?
- The user’s machine used a legit windows binary to download a file from the C2 server. What is the name of the binary?
- The infected machine connected with a famous filesharing site in this period, which also acts as a C2 server used by the malware authors to communicate. What is the name of the filesharing site?
- What is the full URL of the C2 to which the infected host is connected?
- A file was accessed on the filesharing site. What is the name of the file accessed?
- The file contains a secret code with the format THM{_____}.



### How many events were returned for the month of March 2022?

For the first task, we have to filter our log, so that we get the hits from the month `March 2022`. 

For that we enter:
- the start date `Mar 1,2022 @00:00:00.00`
- the end date `Mar 31,2022 @23:59:59.00`#


![ELK-01](assets/img/tryhackme/Elk/elk-01.png "Elk-01")



Answer: `1482`


### What is the IP associated with the suspected user in the logs?

We filter our log a bit and check the `Source IP Address` and the `User-Agent`. 
Bitadmin as `User-Agent` seems suspicious. Lets check which `IP address` is used with this `bitsadmin`


    Bitsadmin is a command-line tool used to create, download or upload jobs, and to monitor their progress. The bitsadmin tool uses switches to identify the work to 
    perform.


![ELK-Filter-01](assets/img/tryhackme/Elk/elk-filter-01.png)
![ELK-Filter-02](assets/img/tryhackme/Elk/elk-filter-02.png)





![ELK-02](assets/img/tryhackme/Elk/elk-02.png "Elk-02")


Answer: `192.166.65.54`


### The user’s machine used a legit windows binary to download a file from the C2 server. What is the name of the binary?


We got this solved with our previous search. 

Answer: `bitsadmin`


### The infected machine connected with a famous filesharing site in this period, which also acts as a C2 server used by the malware authors to communicate. What is the name of the filesharing site


We check again our privios event with `User-Agent` `bitsadmin`.

![ELK-03](assets/img/tryhackme/Elk/elk-03.png "Elk-03")


Answer:`pastebin.com`


### What is the full URL of the C2 to which the infected host is connected?

We need to check the `uri` field here and add the pieces together.

`pastebin.com` + `/yTg0Ah6a` = `pastebin.com/yTg0Ah6a`


![ELK-04](assets/img/tryhackme/Elk/elk-04.png "Elk-04")


Answer: `pastebin.com/yTg0Ah6a`


###  A file was accessed on the filesharing site. What is the name of the file accessed?


Normally you always use a `sandbox` for the next question, but we will have a look, where the link will lead us.

We enter `pastebin.com/yTg0Ah6a` into the `address bar` and we got the answer.


![ELK-05](assets/img/tryhackme/Elk/elk-05.png "Elk-05")


Answer: `secret.txt`


### The file contains a secret code with the format THM{_____}.


Answer: `THM{SECRET__CODE}`



## Thoughts:

I really like to use `ELK` as `SIEM` solution! The `KQL` language and the `UIX` makes this tool very easy to use! I think this task is a little bit too basic but it is nice to practise the tool in a `scenario`.

What did we learn?
- using `ELK` for filtering logs
- using different filters to obtain the required data