## User role can be modified in user profile

This lab has an admin panel at /admin. It's only accessible to logged-in users with a roleid of 2.

Solve the lab by accessing the admin panel and using it to delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /admin
- /login


## Resume

1. Login on wiener account
2. Capture a **POST** change mail request
3. Update it with `"roleid":2`
4. Access admin portal
5. Delete Carlos's account


## Solve 

Login with wiener:peter and capture a **POST** change mail request, the request answer : 

```json
{
  "username": "wiener",
  "email": "toto@toto.com",
  "apikey": "MvEf2FnX9934ZQmvNhIOdpOpwnYK4ap6",
  "roleid": 1
}
```

Update the **POST** request with the `roleid=2` and send it again

```json
{"email":"toto@toto.com",
"roleid":2}
```

Access the `/admin` and delete Carlos's account