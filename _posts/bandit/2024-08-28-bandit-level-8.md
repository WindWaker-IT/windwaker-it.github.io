---
title: "Bandit: Level 8 - Level 9"
date: 2024-08-28 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 8

**Username:** `bandit8`  
**Password:** `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit9.html

  
    The password for the next level is stored in the file data.txt and is the only line of text that occurs only once

    Commands you may need to solve this level
    grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd


<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit8` on port `2220`.

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

Checking the commands above we need something to filter out a line of text which just appears once. :) 

`uniq` and `sort` looking promising.

Lets check the manual entrys:

```bash

SORT(1)                                                                    User Commands                                                                   SORT(1)

NAME
       sort - sort lines of text files

SYNOPSIS
       sort [OPTION]... [FILE]...
       sort [OPTION]... --files0-from=F

DESCRIPTION
       Write sorted concatenation of all FILE(s) to standard output.
``` 

Okay so we can sort line of text files with `sort`. What about `uniq`? 
```bash
   -u, --unique
              only print unique lines
```

Lets try to send the output from `sort` to `uniq`.

```bash
bandit8@bandit:~$ sort data.txt |uniq -u
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

Perfect! We got the password:

Password: `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM` 

## Explanation: 

#### Understanding the `uniq` Command

The `uniq` command in Linux is used to process input by removing or filtering out duplicate lines. Here are some key features:

- **Filter for Unique Lines**: By default, `uniq` removes duplicate lines from the input, but with the `-u` flag, it can display only lines that occur exactly once.

- **Count Occurrences**: Using the `-c` option, `uniq` can prepend a count of occurrences to each line, showing how many times each line appears.

- **Show Duplicates**: The `-d` flag makes `uniq` output only the lines that are duplicated in the input.

#### Common Usage with `sort`

The `uniq` command is often used in conjunction with `sort` because `uniq` relies on the input being sorted to correctly identify duplicates. 

- **Sorting Lines**: The `sort` command arranges lines of text in a specific order. It includes options for sorting in reverse (`-r`) or numerically (`-n`).

When you use `sort` followed by `uniq`, you ensure that all duplicate lines are adjacent, allowing `uniq` to effectively filter or count them.

Example:

```bash
sort file.txt | uniq -c
