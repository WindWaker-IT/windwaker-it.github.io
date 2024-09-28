---
title: "[SOC-Level 1] Tryhackme - Friday Overtime"
date: 2024-09-28 12:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, Friday Overtime]
tags: [soc,tryhackme,blueteam]
author: Christoph K
---
[TryHackMe- Agent Sudo ](https://tryhackme.com/r/room/fridayovertime)



## Scenario:

    Disclaimer

    Please note: The artefacts used in this scenario were retrieved from a real-world cyber-attack. Hence, it is advised that interaction with the artefacts be done only inside the attached VM, as it is an isolated environment.


    Hello Busy Weekend. . .

    It's a Friday evening at PandaProbe Intelligence when a notification appears on your CTI platform. While most are already looking forward to the weekend, you realise you must pull overtime because SwiftSpend Finance has opened a new ticket, raising concerns about potential malware threats. The finance company, known for its meticulous security measures, stumbled upon something suspicious and wanted immediate expert analysis.

    As the only remaining CTI Analyst on shift at PandaProbe Intelligence, you quickly took charge of the situation, realising the gravity of a potential breach at a financial institution. The ticket contained multiple file attachments, presumed to be malware samples.

    With a deep breath, a focused mind, and the longing desire to go home, you began the process of:

    Downloading the malware samples provided in the ticket, ensuring they were contained in a secure environment.
    Running the samples through preliminary automated malware analysis tools to get a quick overview.
    Deep diving into a manual analysis, understanding the malware's behaviour, and identifying its communication patterns.
    Correlating findings with global threat intelligence databases to identify known signatures or behaviours.
    Compiling a comprehensive report with mitigation and recovery steps, ensuring SwiftSpend Finance could swiftly address potential threats.
    Connecting to the machine

    Start the virtual machine in split-screen view by clicking the green Start Machine button on the upper right section of this task. If the VM is not visible, use the blue Show Split View button at the top-right of the page. Additionally, you can open the DocIntel platform using the credentials below.

    THM key
    Username	ericatracy
    Password	Intel321!
    IP	10.10.99.22

    Note: While the web browser (i.e., Chromium) will immediately start after boot up, it may show tabs that have "Connection Refused" displayed. This is because the DocIntel platform takes a few more minutes to finish starting up after the VM has completely booted up. The ticket details can be found by logging in to the DocIntel platform. OSINT, a web browser, and a text editor outside the VM will also help.



## Tasks
- Who shared the malware samples?
- What is the SHA1 hash of the file "pRsm.dll" inside samples.zip?
- Which malware framework utilizes these DLLs as add-on modules?
- Which MITRE ATT&CK Technique is linked to using pRsm.dll in this malware framework?
- What is the CyberChef defanged URL of the malicious download location first seen on 2020-11-02?
- What is the CyberChef defanged IP address of the C&C server first detected on 2020-09-14 using these modules?
- What is the SHA1 hash of the spyagent family spyware hosted on the same IP targeting Android devices on November 16, 2022?


### Who shared the malware samples?

After connecting to the VM and login into our CTI platform, we can read the message from `SwiftSpend Finance` regarding the malware.

`Message`: 

    Dear PandaProbe Intel team,

    I hope this message finds you well. My name is Oliver Bennett from the Cybersecurity Division at SwiftSpend Finance. During our recent security sweep, we have identified a set of malicious files which, based on our preliminary analysis, seem to be associated with .

    Details

    Date Detected: Friday, December 8, 2023

    Infected Systems: Over 9000 systems

    Nature of Malware: Unknown / Suspected RAT

    We believe the intent of this malware is to gain a foothold to ultimately exfiltrate sensitive financial data and possibly deploy ransomware.

    Immediate Actions Taken

    - Isolated the infected systems from the network.

    - Initiated a comprehensive scan across all systems.

    - Collected and stored malware samples securely for further analysis.

    - We are currently collaborating with external cybersecurity agencies and our security solutions providers to get a deeper understanding of this malware. However, we wanted to raise this with you immediately given the potential risk associated with APTs.

    We strongly need your team's assistance with conducting a thorough review of the malware sample. The password to the attached archive is: Panda321!

    Moving forward, we are going to conduct a User Awareness Training to inform all staff members to be extra cautious, especially when dealing with email attachments and links.

    Attached are the indicators of compromise (IoCs) for your perusal. I am also available for a call or meeting to discuss our findings in detail and strategise our response.

    Your prompt attention to this matter is highly appreciated. Let's work together to ensure the safety and integrity of our systems and data.

    Warm regards,

    Oliver Bennett

    Cybersecurity Division

    SwiftSpend Finance

    Phone: +123 456 7890

    Email: oliver.bennett@swiftspend.finance


Okay after reading carefully the mail here, we also know that `Oliver Bennett` sent us the samples.

Answer: `Oliver Bennett`



### What is the SHA1 hash of the file "pRsm.dll" inside samples.zip?

For the next question, we have to download the files from Oliver and extract them. 
We have following file: 

```bash

[ericatracy@ip-10-10-46-93 Downloads]$ ls -la
total 2576
drwx------  2 ericatracy ericatracy     102 Sep 28 02:42 .
drwx------ 12 ericatracy ericatracy     320 Sep 28 02:38 ..
-rw-rw-r--  1 ericatracy ericatracy  160768 May  4  2023 cbmrpa.dll
-rw-rw-r--  1 ericatracy ericatracy  108544 May  4  2023 maillfpassword.dll
-rw-rw-r--  1 ericatracy ericatracy  124416 May  4  2023 pRsm.dll
-rw-rw-r--  1 ericatracy ericatracy  204288 May  4  2023 qmsdp.dll
-rw-rw-r--  1 ericatracy ericatracy 1257984 May  4  2023 wcdbcrk.dll
```

Lets get the `SHA1`hash from the file:



```bash

[ericatracy@ip-10-10-46-93 Downloads]$ sha1sum pRsm.dll 
9d1ecbbe8637fed0d89fca1af35ea821277ad2e8  pRsm.dll
[ericatracy@ip-10-10-46-93 Downloads]$ 
```

Answer: `9d1ecbbe8637fed0d89fca1af35ea821277ad2e8`


### Which MITRE ATT&CK Technique is linked to using pRsm.dll in this malware framework?

Now we can do a little research. I tried `abuse.ch` and `virustotal` and found out:

![virustotal](assets/img/tryhackme/friday-overtime/friday-overtime.png "virustotal")

Short research about `mgbot`

    MgBot
    MgBot is the primary Windows backdoor used by Evasive Panda, which according to our findings has existed since at least 2012 and, as mentioned in this blog post, was publicly documented at VirusBulletin in 2014. It was developed in C++ with an object-oriented design, and has the capabilities to communicate via TCP and UDP, and extend its functionality via plugin modules.

    MgBot’s installer and backdoor, and their functionality, have not changed significantly since it was first documented. Its chain of execution is the same as described in this report by Malwarebytes from 2020.




Answer: `mgbot`


### Which MITRE ATT&CK Technique is linked to using pRsm.dll in this malware framework?


[https://www.welivesecurity.com/2023/04/26/evasive-panda-apt-group-malware-updates-popular-chinese-software/](https://www.welivesecurity.com/2023/04/26/evasive-panda-apt-group-malware-updates-popular-chinese-software/)



[T1123-ATT&CK](https://attack.mitre.org/techniques/T1123/)


In the same article, we can find out, what our `dll` does.

    T1123	Audio Capture	MgBot’s plugin module pRsm.dll captures input and output audio streams.


Answer: `T1123`



### What is the CyberChef defanged URL of the malicious download location first seen on 2020-11-02?


Okay, we have to do some more research here. In the article, you can find the following text:

![welivesecurity](assets/img/tryhackme/friday-overtime/friday-overtime-1.png "welivesecurity")

[https://www.welivesecurity.com/2023/04/26/evasive-panda-apt-group-malware-updates-popular-chinese-software/](https://www.welivesecurity.com/2023/04/26/evasive-panda-apt-group-malware-updates-popular-chinese-software/)



Now we have to `defang` the URL with `Cyberchef`:


[https://gchq.github.io/CyberChef/](https://gchq.github.io/CyberChef/)



Answer: `hxxp[://]update[.]browser[.]qq[.]com/qmbs/QQ/QQUrlMgr_QQ88_4296.exe`




### - What is the CyberChef defanged IP address of the C&C server first detected on 2020-09-14 using these modules?

And again researching: 

![welivesecurity](assets/img/tryhackme/friday-overtime/friday-overtime-2.png "welivesecurity")



Answer: `122[.]10[.]90[.]12`



### - What is the SHA1 hash of the spyagent family spyware hosted on the same IP targeting Android devices on November 16, 2022?


In Virustotal there is the TAB `Relation`. There I found a file for android, which is related. After checking the `SHA1`from this file, I found out, that it targeted Android devices on November 16, 2022.

![virustotal](assets/img/tryhackme/friday-overtime/friday-overtime-3.png "virustotal")

Important, you have to take the `SHA1`hash from the file!


Answer: `1c1fe906e822012f6235fcc53f601d006d15d7be`


## Thoughts:

I love to do some research and finally use all the cool OSINT tools. Additional to that, we had a "real malware" for analysing, kind of a "real world scenario", which I really appreciate.


What did we learn?
- `Research` with `ATT&CK` 
- `Research` with `Virustotal`
- Analyzing behavior and reports