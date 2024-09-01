---
title: "Bandit: Level 11 - Level 12"
date: 2024-08-28 14:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 11

**Username:** `bandit11`  
**Password:** `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit12.html

    The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

    Commands you may need to solve this level
    grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

    Helpful Reading Material
    Rot13 on Wikipedia

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit11` on port `2220`.

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

Okay I think we have to do a little research on `Rot13`. 

![wiki](assets/img/Bandit_11/Bandit_11_1.png "wiki")

This helpful table is a good visual what we have to do here. We have to translate the letters 13 positions back. The `tr`command looks like the correct choice here. Lets check the manual.

The manual is a little bit difficult to understand here:

```bash

NAME
       tr - translate or delete characters

SYNOPSIS
       tr [OPTION]... STRING1 [STRING2]

DESCRIPTION
       Translate,  squeeze, and/or delete characters from standard input, writing to standard output.  STRING1 and STRING2 specify arrays of characters ARRAY1 and
       ARRAY2 that control the action.


``` 

What is meant with that? 
- **OPTION** Optional flags 
- **STRING1**  is the set of characters what we want to translate/delete.
- **STRING2** is the set of characters we want to translate into


For ROT13 encoding or decoding, you use `tr` to map each letter of the alphabet to its counterpart 13 positions ahead. 

We use **STRING1** for our characters, what we want:

`"A-Za-z"`          is our alphabet with upper and lower letters

We use **STRING2** for our characters, what we want replaced with:
`"N-ZA-Mn-za-m"`    is the ROT13 alphabet, check the picture above. The upper alphabet goes "N-Z + A-M" and the lower alphabet goes "n-z + a-m".



Lets take all this in one command:

```bash
bandit11@bandit:~$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```

We send the ouput of the data.txt file into the "translator" with our prefered translation and get the password as output :joy: 

Password: `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

## Explanation: 
#### Substitution Ciphers

Substitution involves replacing one character with another. Substitution ciphers, such as the Caesar and Vigenère ciphers, have been used for a long time but are no longer considered secure.

The ROT13 cipher, a simple substitution cipher, rotates letters by 13 positions in the alphabet. This is particularly interesting because, with a 26-letter Latin alphabet, the encryption algorithm is the same as the decryption algorithm.

In Linux, the `tr` command, which stands for 'translate', is used for character replacement. The basic syntax for `tr` is:

```bash
tr <old_chars> <new_chars>
