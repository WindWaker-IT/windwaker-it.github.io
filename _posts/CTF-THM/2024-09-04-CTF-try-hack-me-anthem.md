---
title: "[CTF] Tryhackme - Anthem "
date: 2024-09-04 12:00:00 -500 
categories: [CTF - Tryhackme, Anthem]
tags: [ctf,exploit,flags,tryhackme]
author: Christoph K
---




[TryHackMe- Anthem](https://tryhackme.com/r/room/anthem)


# Task 1:
- Let's run nmap and check what ports are open.
- What port is for the web server?
- What port is for remote desktop service?
- What is a possible password in one of the pages web crawlers check for?
- What CMS is the website using?
- What is the domain of the website?
- What's the name of the Administrator
- Can we find find the email address of the administrator?




Lets connect first. You can use the build-in VMs or connect via VPN. I use VPN because I like to work in my own environment.

Our first task is:

#### Let's run nmap and check what ports are open.

 `nmap -sC -sV -Av`
 - `-sC` = run all default scripts
 - `-sV` = find the version of the services
 - `-Av` = Agressive Mode + verbose Mode

```bash
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Anthem.com - Welcome to our blog
| http-robots.txt: 4 disallowed entries 
|_/bin/ /config/ /umbraco/ /umbraco_client/
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-09-10T07:20:58+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: WIN-LU09299160F
|   NetBIOS_Domain_Name: WIN-LU09299160F
|   NetBIOS_Computer_Name: WIN-LU09299160F
|   DNS_Domain_Name: WIN-LU09299160F
|   DNS_Computer_Name: WIN-LU09299160F
|   Product_Version: 10.0.17763
|_  System_Time: 2024-09-10T07:20:49+00:00
| ssl-cert: Subject: commonName=WIN-LU09299160F
| Issuer: commonName=WIN-LU09299160F
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-09-09T06:40:07
| Not valid after:  2025-03-11T06:40:07
| MD5:   4a25:f7d8:5d7d:5580:7fbf:663b:227d:2658
|_SHA-1: 3ded:44d2:571f:c82d:0075:806c:4aec:3c7c:850f:b4ca
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

As we can see above, we could find 2 open ports and the services behind it. We have port `80` for the service `http` and port `3389` for `RDP`. We also found some interesting directories and a file `robots.txt`.

So we can also answer the second and third question:

### What port is for the web server

Answer: `Port 80`

### What port is for remote desktop service?

Answer: `Port 3389`

### What is a possible password in one of the pages web crawlers check for?

- Okay what are web crawlers? 

A web crawler is a program that automatically visits websites and reads through their pages to collect information. Think of it like a robot that explores websites to see what’s there.

Good then we have to know, what files web crawler check:

- robots.txt - This file tells web crawlers which parts of the website they are allowed or not allowed to index. Sometimes, it may reference directories or files that contain sensitive information.

- htaccess -  If accessible, it could contain restricted access details.
- sitemap.xml - It lists all the pages on the website for search engines. There might be some obscure or hidden pages listed there.


We found in our `nmap` scan already the `robots.txt` file, lets have a look:


```bash
└─$ curl 10.10.211.67/robots.txt
UmbracoIsTheBest!

# Use for all search robots
User-agent: *

# Define the directories not to crawl
Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/
```

The line `UmbracoIsTheBest!` looks like a password.

Answer: `UmbracoIsTheBest!`


### What CMS is the website using?


We can see the name of the CMS already in the `robots.txt` file. If you check the directory `/umbraco/` you find a login page.

Answer: `umbraco`

### What is the domain of the website? 

Once opened the webpage in browser you can find the title: `anthem.com`. 


Answer: `anthem.com`

### What is the name of the administrator?

Lets check the contents of the webpage. There is a hint on one of the pages:

    # A cheers to our IT department

    Tuesday, December 31, 2019

    During our hard times our beloved admin managed to save our business by
    redesigning the entire website.

    As we all around here knows how much I love writing poems I decided to write
    one about him:

    Born on a Monday,  
    Christened on Tuesday,  
    Married on Wednesday,  
    Took ill on Thursday,  
    Grew worse on Friday,  
    Died on Saturday,  
    Buried on Sunday.  
    That was the end…

    Share this post

A google search tells us, that the author of this poem is Solomon Grundy.

Answer: `Solomon Grundy`

### Can we find find the email address of the administrator?

We have to check the webpages again. In the other post, there is a mail addresse from another user. 


    Hi fellow readers,

    We are currently hiring. We are looking for young talented to join a good cause and keep this community alive!

    If you have an interest in being a part of the movement send me your CV at JD@anthem.com

So, if we just use the same pattern from `JD@anthem.com` then we can use the first and last name letters from our `Solomon Grundy` to guess the mail address: SG@anthem.com

Answer: `SG@anthem.com`



Nice, we got all our answers from ***TASK 1*** 


# Task 2:

- Flag 1
- Flag 2
- Flag 3
- Flag 4



### Flags:

a "flag" is a specific piece of information (usually a string of text) that you need to find to solve a challenge. The flag serves as proof that you've successfully completed the challenge.

Most of the time the format is: `CTF{some_text}`



## Flag 1 + 2:

**hint:  Inspect the webpages**

```html
            <h2 class="blog-description">
        Welcome to our blog
    </h2>
            <nav class="menu" role="nav">
        <ul>
            <li><a href="/blog/categories">Categories</a></li>
            <li><a href="/blog/tags">Tags</a></li>
            <li>
                <div class="articulate-search">
        <form method="get" action="/blog/search">
            <input type="text" name="term" placeholder="Search...                                                           THM{G!T_G00D}" />
            <button type="submit" class="fa fa-search fa"></button>
        </form>
    </div>
            </li>
        </ul>
    </nav>

        </header>

        

    <main class="content" role="main">

        <article class="post">

            <header>
```

We know we search for `THM`flags, thats why we can use `curl` to search for the word `THM` in other pages.
```bash
    ─$ curl http://10.10.211.67/archive/we-are-hiring/ |grep THM                                                                                                                                                                              
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
    0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0<meta content="THM{L0L_WH0_US3S_M3T4}" property="og:description" />
            <input type="text" name="term" placeholder="Search...                                                           THM{G!T_G00D}" />
    100  6200  100  6200    0     0   7932      0 --:--:-- --:--:-- --:--:--  7928
```



Nice, we found already two flags:

`THM{G!T_G00D}` (flag 2) 
`THM{L0L_WH0_US3S_M3T4}` (flag 1)


## Flag 3:

**hint: Profile** 

I ran a `gobuster` seach before to find some hidden directories. 
```bash 
─$ gobuster -u 10.10.211.67 -w ~/Downloads/common.txt dir
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.211.67
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/admin/Downloads/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/Archive              (Status: 301) [Size: 118] [--> /]
/Blog                 (Status: 200) [Size: 5394]
/RSS                  (Status: 200) [Size: 1873]
/Search               (Status: 200) [Size: 3468]
/SiteMap              (Status: 200) [Size: 1041]
/archive              (Status: 301) [Size: 123] [--> /blog/]
/authors              (Status: 200) [Size: 4115]
Progress: 339 / 1943 (17.45%)[ERROR] Get "http://10.10.211.67/aux": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
/blog                 (Status: 200) [Size: 5394]
/categories           (Status: 200) [Size: 3541]
Progress: 896 / 1943 (46.11%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 896 / 1943 (46.11%)
===============================================================
Finished
===============================================================
```

I think `authors` looks promising. When we use the browser for checking /authors we find the next flag under `webpage`. 

Flag 3:  `THM{L0L_WH0_D15}`

## Flag 4:

**Hint: Have we all inspected all the pages yet?**

For the last flag I used `curl` and searched every directory of the webpage with `THM`. I found it.
```bash 
curl http://10.10.211.67/archive/a-cheers-to-our-it-department  |grep THM
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  6260  100  6260    0     0   8264      0 --:--:-- --:--:-- --:--:--  8258
<meta content="THM{AN0TH3R_M3TA}" property="og:description" />
        <input type="text" name="term" placeholder="Search...    
```


Flag 4: `THM{AN0TH3R_M3TA}`


We have all our flags now.


# Task 3:

- Gain initial access to the machine, what is the contents of user.txt?
- Can we spot the admin password?
- Escalate your privileges to root, what is the contents of root.txt?


### Gain initial access to the machine, what is the contents of user.txt?


For that I use `rdesktop` on Kali Linux and connect to the server. As username I use `SG` and `UmbracoIsTheBest!`. On the desktop is the user.txt file.

![RDP](assets/img/tryhackme/Anthem/anthem_1.png "RDP_01")


Answer: `THM{N00T_NO0T}`



###  Can we spot the admin password?

**Hint: Hidden**


Lets change the explorer view to `show hidden files` and start with `C:\`. Oh - there is a hidden folder `backup`. When we open the `restore.txt` we get a message, that we are not the owner and cant open it. We can easily change that. 


![RDP](assets/img/tryhackme/Anthem/anthem_2.png "RDP_2")


Password: `ChangeMeBaby1MoreTime`



###  Escalate your privileges to root, what is the contents of root.txt?

The last task shouldnt be a problem anymore. We can just open a `cmd` as Administrator and use the password from the last question or we just open a new RDP Session with the Administrator. 

On the Desktop you can find a text file with following content:


`THM{Y0U_4R3_1337}`

## Thoughts:

Very good entry CTF for getting to know `ports` and `services` as well as some `source code`from webpages.
What did we learn?
- using `nmap` for finding services and ports
- information about webpages (web scrapper)
- searching in `source code` 