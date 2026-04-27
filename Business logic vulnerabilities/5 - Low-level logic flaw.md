## Low-level logic flaw

This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price. To solve the lab, buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter

Hint :

You will need to use Burp Intruder (or Turbo Intruder) to solve this lab.

To make sure the price increases in predictable increments, we recommend configuring your attack to only send one request at a time. In Burp Intruder, you can do this from the resource pool settings using the Maximum concurrent requests option.

## Notes

- /login
- /cart


## Resume

1. Login with **wiener:peter**
2. Capture a **POST** `/cart` request adding the **"Lightweight l33t Leather Jacket"**
3. Send it to **Intruder** using **Null payloads**
4. Reduce the price enough to purchase the item


## Solve

Login with **wiener:peter** and perform a normal workflow by attempting to buy the **"Lightweight l33t Leather Jacket"**. Then send the **POST** `/cart` request to Intruder:

```
POST /cart HTTP/2
Host: 0ab4000703fbf0cb81cea36500a00003.web-security-academy.net
Cookie: session=5NFIa3EQwF8Q8iByFnFmG8RjNAVmcWCS
Content-Length: 37
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0ab4000703fbf0cb81cea36500a00003.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ab4000703fbf0cb81cea36500a00003.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

productId=1&redir=PRODUCT&quantity=99
```

There is no validation on the `quantity` parameter. The maximum value we can add to our cart is **99**. By repeatedly sending the same request using **Intruder** with **Null payloads**, the total cart price increases until it reaches its limit, overflows into a negative value, and eventually cycles back toward **0**.

This means that with enough **POST** requests to `/cart` using `quantity=99`, we can manipulate the total price and make expensive items affordable, such as the **"Lightweight `l33t` Leather Jacket"**.

To ensure proper sequencing, add the attack to a **resource pool** with **Maximum concurrent requests set to 1**.

---

### Step 1 — Trigger the overflow

By sending **162** requests with `quantity=99`, the total price becomes:

`-$21374503.96`

---

### Step 2 — Bring the total back toward 0

We calculate how many jackets are needed to offset the negative value:

`21374503.96 / 1337 ≈ 15986.91`

So we need **15987** jackets.

Now, we determine how many full requests we can send:

`15987 / 99 ≈ 161.48`

This means we need:

- **161 requests** with `quantity=99`
- **1 final request** with `quantity=47`

---

### Step 3 — Final state after adjustment

In total:

- **162 + 161 = 323 requests** using **Null payloads**
- **1 final request** with `quantity=47`

This results in:

- Total price: **-$1221.96**
- Total jackets: **32123**

At this stage, adding more jackets would cause the total to exceed **$100**, which is our store credit limit.

---

### Step 4 — Adjust the final price

To bring the total just below **$100**, we can add another product.

For example, the **"The Trolley-ON"**, which costs **$96.89**:

`1221.96 / 96.89 ≈ 12.6`

- Adding **12** units → total: **-$59.28**
- Adding **13** units → total: **$37.61**

This final total is below **$100**, allowing us to purchase the **"Lightweight l33t Leather Jacket"**.

---

## Conclusion

By exploiting the lack of validation on the `quantity` parameter and leveraging integer overflow behavior, we can manipulate the cart total and bypass pricing restrictions.

