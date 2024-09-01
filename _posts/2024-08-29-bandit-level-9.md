---
title: "Bandit: Level 9 - Level 10"
date: 2024-08-28 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 9

**Username:** `bandit9`  
**Password:** `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit10.html

  
    The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

    Commands you may need to solve this level
    grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd


<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit9` on port `2220`.

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

Another look to the commands we can/should use and this time the command `strings` is popping off. It prints sequences of printable characters in files, which sounds perfect. 

We learned already to use `|` and `grep` to send output and search for keywords. 


Lets try it:
```bash
bandit9@bandit:~$ strings data.txt |grep ===
\a!;========== the
========== passwordf
========== isc
========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

Alright, we got the passwort.

Password: `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`


## Explanation: 

### Understanding the `strings` Command

The `strings` command in Linux is used to extract and display sequences of printable characters from binary files. This is particularly useful for examining non-text files such as hex dumps or executable files. 

### Key Points:

- **Extract Printable Text**: `strings` scans a file and outputs sequences of printable characters, which helps in identifying readable content within binary or non-printable files.

- **Common Use Cases**: It is often used to search through files like executables or dumps to find embedded text or debug information that may not be immediately visible in a raw binary format.

By using `strings`, you can reveal human-readable text within files that would otherwise appear as a jumble of non-printable characters.
