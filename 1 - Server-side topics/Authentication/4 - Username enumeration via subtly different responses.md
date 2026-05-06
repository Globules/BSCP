## Username enumeration via subtly different responses

This lab is subtly vulnerable to username enumeration and password brute-force attacks. It has an account with a predictable username and password, which can be found in the following wordlists:

Candidate usernames
Candidate passwords

To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.

## Notes

- Possibility to leave a comment on a post
- /login

## Resume

1. Capture login request
2. Send it to intruder using **sniper attack** + **Grep - Extract** to get username
3. **sniper attack** to bruteforce username
4. Login with credential

## Solve 

Try with random credentials to login and send the request to intruder. With a failed attempt to login we have a message : **Invalid username or password.** that we will use later.

```
POST /login HTTP/2
Host: 0a29009a032436b580dfda03005600a3.web-security-academy.net
Cookie: session=WPTTNeH9FlqXXy940cJOk00VoID5HxGN
Content-Length: 30
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0a29009a032436b580dfda03005600a3.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a29009a032436b580dfda03005600a3.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


username=wiener&password=peter
```

Set a **sniper attack** for the username. In intruder, go to **Settings** and add a new rule in **Grep - Extract** : click add -> highlight **Invalid username or password.** -> OK -> run the attack. 

Once it's over, filter using the grep - extract rule, there is only one diffrent : 

`Invalid username or password` instead of `Invalid username or password.`

Change the username with the one found. Then modify the payload trying to bruteforce the password now. Rerun the **sniper attacker** and filter with status code. Look for a **302** (redirect to profil page).
