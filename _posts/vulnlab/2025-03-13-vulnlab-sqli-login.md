---
title: "[Vulnlab] SQL Injection - Login"
date: 2025-03-13 15:00:00 -500 
categories: [Vulnlab, SQL Injection - Login]
tags: [soc,redteam,sqli,docker,vulnlab]
author: Christoph K
---

# Situation:

We have the following `login form` and access to the `php code`. 

![Login](assets/img/vulnlab/sqli/login/01.png)



```php

<?php
require("../../../lang/lang.php");
$strings = tr();

$mysqli = new mysqli('localhost', 'sql_injection', '', 'sql_injection');

if ($mysqli->connect_errno) {
printf("Connect failed: %s\n", $mysqli->connect_error);
exit();
}

session_start();

if(isset($_POST['username']) && isset($_POST['password']) ){
    
    $usr=$_POST['username'];
    $pwd=$_POST['password'];

    $sql = "SELECT username,password FROM users WHERE username='" . $usr . "' AND password='" . $pwd . "'";


if ($result = $mysqli->query($sql)) {
while($obj = $result->fetch_object()){
    $_SESSION['username'] = $usr;
    header("Location: admin.php");
    exit;
}
}

elseif($mysqli->error){
print($mysqli->error);
}        
    }    
```

## Understand how the login form works


If we check the code, we can find this line:

`$sql = "SELECT username,password FROM users WHERE username='" . $usr . "' AND password='" . $pwd . "'";
`

This query takes user-supplied $usr and $pwd values and places them directly into the SQL statement without sanitization.


## SQLi: Authentication Bypass

After understanding, that there is no sanitization on the variables `$usr`and `$pwd` we can just try following statement:

`' OR '1'='1' -- `

Lets try to understand the logic here:

- We use `'`to escape the statement. 
- We use `or '1'=1` because this is always true. 
- We use `--` because that makes everything after `--` a comment


That makes our `SQL Query` like that: 

```sql
SELECT username,password FROM users WHERE username='' OR '1'='1' -- AND password='x';
```


![Login2](assets/img/vulnlab/sqli/login/02.png)


And we succesfully bypassed authentication!

![Login3](assets/img/vulnlab/sqli/login/03.png)



