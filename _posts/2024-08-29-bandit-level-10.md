---
title: "Bandit: Level 10 - Level 11"
date: 2024-08-28 13:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 10

**Username:** `bandit10`  
**Password:** `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit11.html

    The password for the next level is stored in the file data.txt, which contains base64 encoded data

    Commands you may need to solve this level
    grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd


<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit10` on port `2220`.

We have several commands available for this task:


    man (1)              - an interface to the system reference manuals
    grep (1)             - print lines that match patterns
    sort (1)             - sort lines of text files
    uniq (1)             - report or omit repeated lines
    strings (1)          - print the sequences of printable characters in files
    base64 (1)           - base64 encode/decode data and print to standard output
    tr (1)               - translate or delete characters
    tar (1)              - an archiving utility
    gzip (1)             - compress or expand files
    bzip2 (1)            - a block-sorting file compressor, v1.0.8
    xxd (1)              - make a hex dump or do the reverse.


<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

Okay, we know that `data.txt` is encoded in **base64** so we have to decode that document.

Lets check the manual page from `base64`

```   Base64 encode or decode FILE, or standard input, to standard output.

       With no FILE, or when FILE is -, read standard input.

       Mandatory arguments to long options are mandatory for short options too.

       -d, --decode
              decode data
```


Okay so we have to use `-d` for decoding.

Lets try it:

```bash

bandit10@bandit:~$ base64 -d data.txt
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
``` 

That was quick - we have our password :) 

Password:`dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`



## Explanation: 

### Understanding Base64 Encoding and the `base64` Command

Base64 is a method of encoding binary data into ASCII text format. This encoding is often recognizable by the equal signs (`=`) that appear at the end of the encoded data, though this is not always the case.

In Linux, the `base64` command is used to encode and decode data in Base64 format:

**Encoding Data**: To convert binary data into Base64 format, simply use:

```bash
  base64 input_file
```
 **Decoding Data**: Like in our example above to decode Base64 into ASCII 
   ```bash
  base64 -d input_file
```
