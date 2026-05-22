## Authentication bypass via OAuth implicit flow

This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the client application makes it possible for an attacker to log in to other users' accounts without knowing their password.

To solve the lab, log in to Carlos's account. His email address is carlos@carlos-montoya.net.

You can log in with your own social media account using the following credentials: wiener:peter.

## Notes

- Possibility to leave a comment on a post
- OAuth Server Domain
- /social-login
- /authenticate
- /oauth-callback

## Resume

1. Complete OAuth login flow
2. Intercept callback requests in Burp
3. Identify `/authenticate` request
4. Observe that identity is client-controlled
5. Modify email + username
6. Reuse valid OAuth token
7. Gain access as another user (Carlos)

## Solve

While proxying traffic through Burp Suite, complete the OAuth login process normally.

After authentication, the application sends a request to:

```
POST /authenticate
```

This request is responsible for creating the local session based on OAuth-provided identity data.

Example intercepted request:

```
POST /authenticate HTTP/2
Content-Type: application/json

{
  "email": "wiener@hotdog.com",
  "username": "wiener",
  "token": "fIZDgh57YZJRAewR5zuoSiam3zr3Bm5deZbcOpn_nwc"
}
```

At this stage:
- the `token` is valid
- but the application incorrectly trusts `email` and `username`

There is no server-side validation ensuring that:
- the email corresponds to the OAuth token
- the username matches the authenticated identity

Because the backend blindly trusts these fields, you can directly modify them.

Change:

- `email`
- `username`

to target user credentials:

- `carlos@carlos-montoya.net`
- `carlos`

while keeping the same OAuth token.

Modified request:

```
POST /authenticate HTTP/2
Content-Type: application/json

{
  "email": "carlos@carlos-montoya.net",
  "username": "carlos",
  "token": "fIZDgh57YZJRAewR5zuoSiam3zr3Bm5deZbcOpn_nwc"
}
```

After sending the modified request:

- the application creates a session for **Carlos**
- the OAuth token is still accepted
- identity binding is bypassed
- authentication is fully compromised

In Burp Suite:

- Right-click the `/authenticate` request
- Select **“Request in browser” → “In original session”**
- Open the generated URL

You are now logged in as: **Carlos**

