## JWT authentication bypass via kid header path traversal

This lab uses a JWT-based mechanism for handling sessions. In order to verify the signature, the server uses the kid parameter in JWT header to fetch the relevant key from its filesystem.

To solve the lab, forge a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip :

We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.

## Notes



## Resume



## Solve