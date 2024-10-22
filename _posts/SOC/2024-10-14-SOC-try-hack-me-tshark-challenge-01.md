---
title: "[SOC-Level 1] Tryhackme - TShark Challenge I"
date: 2024-10-14 12:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, TShark Challenge I]
tags: [soc,tryhackme,blueteam,tshark]
author: Christoph K
---
[TryHackMe- TShark Challenge I ](https://tryhackme.com/r/room/tsharkchallengesone)

## Task & Introduction

    An alert has been triggered: "The threat research team discovered a suspicious domain that could be a potential threat to the organisation."

    The case was assigned to you. Inspect the provided teamwork.pcap located in ~/Desktop/exercise-files and create artefacts for detection tooling.

    Your tools: TShark, VirusTotal.


### Task 1: What is the full URL of the malicious/suspicious domain address?
        Investigate the contacted domains.
        Investigate the domains by using VirusTotal.
        According to VirusTotal, there is a domain marked as malicious/suspicious.

        What is the full URL of the malicious/suspicious domain address?

        Enter your answer in defanged format.




Okay lets get started. First we investigate the contacted domains. 

We use:

`tshark -r teamwork.pcap -Y "http.request" -T fields -e http.host -e http.request.uri`

- `-r` for read pcap
- `Y` for Display Filter
- `"http.request"`  is the filter expression used here. It filters the packets to only show HTTP request packets
- `-T fields`tells tshark to output specific fields in a structured format instead of the full packet details. This allows you to extract and display just the relevant information
- `-e` specifies which field we want to extract from each packet
- `http.host` is the field that contains the hostname of the requested server
-  `http.request.uri`is the field that contains the URI part of the request


Lets check the output:

```bash

ubuntu@ip-10-10-97-13:~/Desktop/exercise-files$ tshark -r teamwork.pcap -Y "http.request" -T fields -e http.host -e http.request.uri
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/js/script.js?_=1492480834538
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/inc/visit.php
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/inc/login.php
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/suspecious.php
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/shield.png
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/update.php
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/icon_checked.png
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/icon_uncheck.png
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/feedback.png
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/logo.svg
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/font/PayPalSansSmall-Medium.woff2
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/setting.png
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/js/jquery.creditCardValidator.min.js?_=1492480834539
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/arrow.png
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/profile.png
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/js/cc.js?_=1492480834540
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/csc_standard.png
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/
www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	/img/logo_ccVisa.gif
toolbarqueries.google.com	/tbr?client=navclient-auto&ch=63514382238&features=Rank&q=info%3Ahttp%3A%2F%2Fwww.paypal.com4uswebappsresetaccountrecovery.timeseaways.com%2F%23

```

We have a lot of `www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com` URLs and one `toolbarqueries.google.com`. We use `Virustotal to check both.

![Virustotal](assets/img/tryhackme/tshark/tshark-01.png "Virustotal")



Okay that seems to be the malicious/suspicous URL. We have to `defang` it via `Cyberchef`and then we have our Answer:



Answer: `www[.]paypal[.]com4uswebappsresetaccountrecovery[.]timeseaways[.]com/`



### Task 2: When was the URL of the malicious/suspicious domain address first submitted to VirusTotal?

Lets check the `Details` Tab at `Virustotal`:


![Virustotal-2](assets/img/tryhackme/tshark/tshark-02.png "Virustotal-2")

Answer: `2017-04-17 22:52:53 UTC`


### Task 3: Which known service was the domain trying to impersonate?

Okay when we check again the URL: 

`www[.]paypal[.]com4uswebappsresetaccountrecovery[.]timeseaways[.]com/`


We can clearly see the Service which the domain tries to impersonate:

Answer: `Paypal`


### Task 4: What is the IP address of the malicious domain?

Of course we use `tshark` for that task. We just adjust our last command a bit and add `-e ip.dst`to our `fields`:


```bash
ubuntu@ip-10-10-97-13:~/Desktop/exercise-files$ tshark -r teamwork.pcap -Y "http.request" -T fields -e http.host  -e ip.dst |awk NF |sort -r |uniq -c
     21 www.paypal.com4uswebappsresetaccountrecovery.timeseaways.com	184.154.127.226
      1 toolbarqueries.google.com	216.58.217.100
```

And we use  `Cyberchef` again on `184.154.127.226` to defang the IP address.

Answer: `184[.]154[.]127[.]226`



### Task 5: What is the email address that was used?

Okay lets bake our command here.

We use: 

`tshark -r teamwork.pcap -Y "ip.addr == 184.154.127.226 && http.request.method == POST" -T fields -e text`

- `-r` for read pcap
- `Y` for Display Filter
- `ip.addr == 184.154.127.226`  is the filter expression used here. It filters only traffic to this ip address
- ` http.request.method == POST` we only want to see `POST`requests for submitting a login for example
- `&&` is the logical `AND`
- `-T fields`tells tshark to output specific fields in a structured format instead of the full packet details. This allows you to extract and display just the relevant information
- `-e` specifies which field we want to extract from each packet
- `text` is the field that contains any text content within the packet



```bash

ubuntu@ip-10-10-97-13:~/Desktop/exercise-files$ tshark -r teamwork.pcap -Y "ip.addr == 184.154.127.226 && http.request.method == POST" -T fields -e text
Timestamps,POST /inc/visit.php HTTP/1.1\r\n,\r\n,Form item: "xBrowser" = "Mozilla FireFox v43",Form item: "xOperatingSystem" = "Linux",Form item: "xPlatForm" = "Desktop Platform"
Timestamps,POST /inc/login.php HTTP/1.1\r\n,\r\n,Form item: "user" = "johnny5alive@gmail.com",Form item: "pass" = "johnny5alive",Form item: "xBrowser" = "Mozilla FireFox v43",Form item: "xOperatingSystem" = "Linux",Form item: "xPlatForm" = "Desktop Platform",Form item: "xTimeZone" = "Mon Apr 17 2017 22:00:35 GMT-0400 (EDT)",Form item: "xResoLution" = "Computer: 1920x1080; Browser inner: 1920x762; Browser outer: 1920x1027",Form item: "xLang" = "en-US"

```

And we can see in this text: 

`"user" = "johnny5alive@gmail.com",Form item: "pass" = "johnny5alive"`

So we have the mail addresse. Lets defang it.

Answer: `johnny5alive[at]gmail[.]com`


Have a look to `TShark-Challenge-02` !! :-) 






