## Infinite money logic flaw

This lab has a logic flaw in its purchasing workflow. To solve the lab, exploit this flaw to buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /cart
- /login
- Gift cards
- Newsletter feature
- Exploit server

## Resume

1. Login with wiener peter
2. signup to the newsletter and get the coupon `SIGNUP30`
3. Buy a **Gift card** using the `SIGNUP30` coupon for 7$
4. Go to the profil page and use the gift card to get 10$
5. Use burp macro to get enough money to buy the  "Lightweight l33t leather jacket"

## Solve 

Login with wiener peter. The use the newsletter features to get the `SIGNUP30`. Once we get it we can now buy a **Gift card** (initial price : 10$). Before completing the purchase, we can apply the `SIGNUP30` coupon to the cart to make the new price to 7$. On the `/my-account` use the gift card code to get **10$**. Each time we repeat the operation we get **+3$**. 

### Automatisation

We will use the burp **macro** in order to repeat every step for us and save some time.

Under **Rule action**s, click **Add** > Run a macro. Under **Select macro**, click Add again to open the **Macro Recorder**.

1. Select the following sequence of requests:

```
POST /cart
POST /cart/coupon
POST /cart/checkout
GET /cart/order-confirmation?order-confirmed=true
POST /gift-card
```

2. In the list of requests, select GET `/cart/order-confirmation?order-confirmed=true`. Click Configure item. In the dialog that opens, click Add to create a custom parameter. Name the parameter gift-card and highlight the gift card code at the bottom of the response. Click OK twice to go back to the Macro Editor.

3. Select the `POST /gift-card` request and click Configure item again. In the Parameter handling section, use the drop-down menus to specify that the gift-card parameter should be derived from the prior response (response 4). Click OK.

4. In the Macro Editor, click Test macro. Look at the response to `GET /cart/order-confirmation?order-confirmation=true` and note the gift card code that was generated. Look at the `POST /gift-card request`. Make sure that the gift-card parameter matches and confirm that it received a 302 response. Keep clicking OK until you get back to the main Burp window.

5. Send the GET `/my-account` request to Burp Intruder. Make sure that Sniper attack is selected.

6. In the Payloads side panel, under Payload configuration, select the payload type Null payloads. Choose to generate 412 payloads.

7. Click on  Resource pool to open the Resource pool side panel. Add the attack to a resource pool with the Maximum concurrent requests set to 1. Start the attack.