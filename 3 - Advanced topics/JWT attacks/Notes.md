## Labs tips

Every lab from JWT authentication has:

- JSON Web Token (JWT) used for authentication/session management  
- Token typically stored in cookies or Authorization header  
- Structure: `header.payload.signature`  
- Base64URL-encoded header and payload  
- Signature verification may be missing or flawed  
- Common attacks on `alg` parameter (e.g. `none`)  
- Weak HMAC secrets or brute-forceable signing keys  
- Misconfigured `jwk` / `jku` headers allowing key injection  
- `kid` header manipulation (e.g. path traversal / key confusion)  
- JWT payload often contains user role / user id claims  
- Privilege escalation by modifying JWT claims  
- Burp Suite (Proxy, Repeater, Decoder) heavily used  
- Manual token editing + replay required for exploitation  

## Link

### BSCP Academy

- [JWT overview](https://portswigger.net/web-security/jwt)

### BSCP Labs

- [JWT authentication bypass via unverified signature](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature)  
- [JWT authentication bypass via flawed signature verification](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-flawed-signature-verification)  
- [JWT authentication bypass via weak signing key](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-weak-signing-key)  
- [JWT authentication bypass via jwk header injection](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jwk-header-injection)  
- [JWT authentication bypass via jku header injection](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jku-header-injection)  
- [JWT authentication bypass via kid header path traversal](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-kid-header-path-traversal)  

### Other resources

- [JWT Introduction (PortSwigger)](https://portswigger.net/web-security/jwt)  
- [JWT Explained Simply (YouTube)](https://www.youtube.com/watch?v=7Q17ubqLfaM)  
- [JWT Security Best Practices](https://auth0.com/blog/a-look-at-the-latest-draft-for-jwt-bcp/)  
- [OWASP JWT Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html)  
- [JWT attacks overview (video)](https://www.youtube.com/watch?v=XVv6mJpFOb0)  