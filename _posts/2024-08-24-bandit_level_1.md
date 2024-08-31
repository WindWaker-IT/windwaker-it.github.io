---
title: "Bandit: Level 1 - Level 2"
date: 2024-08-24 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---


## Bandit: Level 1 - Level 2
### Level 1

**Username:** `bandit1`  
**Password:** `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

### Task:

![Task](assets/img/Bandit_1/Bandit_1.png "Task")

### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit` on port `2220`.

We have several commands available for this task:

- `ls`
- `cd`
- `cat`
- `file`
- `du`
- `find`

### Exploring Commands

Let's see what these commands do:

![Pic2](assets/img/Bandit_1/Bandit_2.png)

It looks like we could use **cat** for printing out the text in our file.


### Solve the level

First, check if the file is there with **ls**.

Now, let's try to print out our file named `-` with the command:

```bash
cat - 
```

Alright, nothing seems to happen. We can cancel that with CTRL + C.

It seems we need to adjust our command to display a file with a special character. Let's do a quick Google search as mentioned in the task.

![Pic3](assets/img/Bandit_1/Bandit_3.png)






Voilá! We found the answer.


Let's try it out:

```bash
cat ./-
```
![Pic4]( assets/img/Bandit_1/Bandit_4.png)


*That worked!* 



Now we have the password for Level 2:

`263JGJPfgU6LtdEvgfWU1XP5yac29mFx`


## Explanation: 

**Special interpretation of `-`:**
In many command-line utilities, the `-` character is a special convention that means "read from standard input" rather than from a file. When you type `cat -`, the command expects you to enter text directly, which it will then display.

**Using ./-:**
By using `./-`, you're explicitly specifying the path to the file. The ./ tells the shell to look in the current directory for a file named -, instead of interpreting it as stdin.
