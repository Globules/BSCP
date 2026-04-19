## User role controlled by request parameter

This lab has an admin panel at /admin, which identifies administrators using a forgeable cookie.

Solve the lab by accessing the admin panel and using it to delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /admin
- Post-login cookies


## Resume

1. Login with wiener
2. Update the cookie to true
3. Access admin panel
4. Delete Carlos account


## Solve 

Login with wiener:peter and go to the account page. Check for the cookies once loged in : 

```json
{'Admin': 'false'}
```

Update it : 

```json
{'Admin': 'true'}
```

Access the `/admin` and delete Carlos's account