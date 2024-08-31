---
title: "Leviathan Level 0 - Level 1"
date: 2024-08-25 12:00:00 -500 
categories: [OvertheWire, Leviathan]
tags: [linux,wargames,leviathan,overthewire]
author: Christoph K
---






## Leviathan: Level 0 - Level 1

### Level 1

**Username:** `leviathan0` 
**Password:** `leviathan0`

### Task:

![Task](assets/img/Leviathan_0/Leviathan_task.png)

### Start

Let's begin by connecting to the server `leviathan.labs.overthewire.org` and logging in with user `leviathan0` on port `2223`.



### Exploring Enviroment

Check the environment first. With `pwd` we can find our where we are.  

With `ls` we check which files and folders are in our current directory. 
It seems nothings here ... but what if there is a hidden directory or file?

With `ls -la` we can check the permissions of all files in this directory.

![Pic1](assets/img/Leviathan_0/Leviathan_1.png)

There is a hidden folder `.backup`.

What could be inside this folder?

```bash
cd .backup
```
and again - what is inside? 
```bash
ls -la
```

We found a file `bookmarks.html`. We can print out a few lines of this file via head

```bash
head bookmarks.html
```
### Solve the level

okay - how can we find out the password for the next level without reading the whole file? 

Yes - we can search for it. Lets try it with the word `password` or `leviathan1`

```bash
cat bookmarks.html |grep "password" 
```

![Pic2](assets/img/Leviathan_0/Leviathan_2.png)

Looks good, we have our password



Now we have the password for Level 1:

`3QJ3TgzHDq`


## Explanation hidden files: 

**Why Hidden Files?**
Hidden files in Linux start with a dot (.) and are used to keep configuration files and system data out of sight. This helps avoid clutter in your working directory and keeps important system files organized.

**How to Find and Explore Hidden Files**

1. **Find Your Location**  
   Use `pwd` to see where you are in the file system.

2. **List All Files**  
   By default, `ls` only shows visible files. To include hidden files, use `ls -la`:
   - `-l` provides a detailed list, including file permissions, ownership, and modification dates.
   - `-a` includes hidden files (those starting with a dot).

3. **Explore Hidden Directories**  
   If you find a hidden directory, like `.backup`, navigate into it with `cd .backup`. Then use `ls -la` again to see the contents of this directory.
