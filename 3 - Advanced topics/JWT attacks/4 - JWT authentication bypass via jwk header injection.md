## JWT authentication bypass via jwk header injection

This lab uses a JWT-based mechanism for handling sessions. The server supports the jwk parameter in the JWT header. This is sometimes used to embed the correct verification key directly in the token. However, it fails to check whether the provided key came from a trusted source.

To solve the lab, modify and sign a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip : 

We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.

## Notes



## Resume



## Solve