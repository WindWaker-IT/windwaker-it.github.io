---
title: "Bandit: Level 7 - Level 8"
date: 2024-08-28 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 7

**Username:** `bandit7`  
**Password:** `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit8.html

    Bandit Level 7 → Level 8
    Level Goal
    The password for the next level is stored in the file data.txt next to the word millionth

    Commands you may need to solve this level
    man, grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd


<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit7` on port `2220`.

We have several commands available for this task:

- `man`
- `grep`
- `sort`
- `uniq`
- `strings`
- `base65`
- `tr`
- `tar`
- `gzip`
- `bzip2`
- `xxd`
<!-- CHANGE COMMANDS IF NECCESSARY -->

## Exploring commands

We got some new commands here, so lets see what they do:

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


### Solve the level

This level should introduce you to piping `|` the output and use `grep`. This is very essential and crucial to "forward the output" via `|` and search via `grep`.


Pipes in Linux allow you to pass the output of one command directly as input to another command, enabling you to combine and process data efficiently in a command-line pipeline.

Lets try it:

```bash 
bandit7@bandit:~$ cat data.txt |grep millionth
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

As you can see, we check the output of `data.txt` with `cat` and pass the output to the `grep`command where we search for the keyword `millionth`. 


Password: `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`



## Explanation: 

#### Using `grep` with Pipes (`|`) in Linux

In the Linux command line, you can use `grep` in combination with pipes (`|`) to filter and search for specific patterns in the output of other commands. Here’s how it works:

- **`grep`**: This command searches for text patterns in files or input provided to it. For example, `grep 'pattern' file.txt` will search for the term "pattern" in `file.txt` and display the matching lines.

- **Pipes (`|`)**: The pipe symbol (`|`) takes the output of one command and sends it as input to another command. This allows you to chain commands together to process data in stages.

