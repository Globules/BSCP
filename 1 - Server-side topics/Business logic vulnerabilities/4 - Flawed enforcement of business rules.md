## Flawed enforcement of business rules

This lab has a logic flaw in its purchasing workflow. To solve the lab, exploit this flaw to buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter

## Notes

- newsletter feature
- /login
- /cart

## Resume

1. Login with wiener:peter
2. Add the "Lightweight l33t leather jacket" to cart
3. Use the newsletter feature to get the `SIGNUP30` reduction code
4. Use the two coupons alternately to reduce the price
5. Buy the "Lightweight l33t leather jacket"

## Solve 

Login in with wiener:peter and add the "Lightweight l33t leather jacket" to your cart. On home page scroll down and type any mail to get the `SIGNUP30` coupon. Then go back on `/cart` and get the `NEWCUST5` coupon.

If you use twice the same coupon you'll have an error "Coupon already applied". But if we use `SIGNUP30` then `NEWCUST5` we can apply both and reduce the price. We just have to repeat the operation with the 2 coupons to lower the price enough to buy the "Lightweight l33t leather jacket". 